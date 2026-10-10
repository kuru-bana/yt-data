# 通信しているapiを抜くコード
```
(function() {
    console.log('%c[API Monitor] 監視を開始しました (Fetch & XHR)', 'color: #fff; background: #222; padding: 4px; font-weight: bold;');

    // ==========================================
    // 1. Fetch API の監視設定
    // ==========================================
    const originalFetch = window.fetch;
    window.fetch = async function(...args) {
        const url = args[0];
        const options = args[1] || {};
        
        console.log(`%c[Fetch API] 🚀 ${options.method || 'GET'}: ${url}`, 'color: #00ff00; font-weight: bold;');
        if (options.body) {
            console.log('  └ 送信(Payload):', options.body);
        }

        try {
            const response = await originalFetch.apply(this, args);
            const clone = response.clone();
            const contentType = clone.headers.get('content-type');
            
            if (contentType && contentType.includes('application/json')) {
                const data = await clone.json();
                console.log('  └ 受信(JSON):', data);
            } else {
                const text = await clone.text();
                console.log('  └ 受信(Text):', text.substring(0, 200));
            }
            return response;
        } catch (error) {
            console.error(`  └ [Fetch Error]:`, error);
            throw error;
        }
    };

    // ==========================================
    // 2. XMLHttpRequest (XHR) の監視設定
    // ==========================================
    const originalOpen = XMLHttpRequest.prototype.open;
    const originalSend = XMLHttpRequest.prototype.send;

    XMLHttpRequest.prototype.open = function(method, url, ...args) {
        this._url = url;
        this._method = method;
        return originalOpen.apply(this, [method, url, ...args]);
    };

    XMLHttpRequest.prototype.send = function(body, ...args) {
        console.log(`%c[XHR] 🚀 ${this._method}: ${this._url}`, 'color: #ff00ff; font-weight: bold;');
        if (body) {
            console.log('  └ 送信(Payload):', body);
        }

        this.addEventListener('load', function() {
            try {
                const data = JSON.parse(this.responseText);
                console.log('  └ 受信(JSON):', data);
            } catch (e) {
                console.log('  └ 受信(Text):', this.responseText.substring(0, 200)); 
            }
        });

        return originalSend.apply(this, [body, ...args]);
    };
})();

```
##### 便利なツール?
```
/*
 * Scratch Studio Multi-Adder
 *
 * 使い方:
 * 1. https://scratch.mit.edu/projects/作品ID を開き、Scratchにログインする
 * 2. 開発者ツールの Console にこのファイルの内容を貼り付けて実行する
 * 3. スタジオIDまたはスタジオURLを登録し、追加先を選んで実行する
 *
 * Scratchの非公式APIを使います。APIの仕様変更やScratch側の権限設定により
 * 動作しなくなる場合があります。ユーザー名・パスワード・セッション情報は保存しません。
 */
(() => {
    "use strict";

    const PANEL_ID = "scratch-studio-multi-adder";
    const STORAGE_KEY = "scratch-studio-multi-adder.studios.v1";
    const API_BASE = "https://api.scratch.mit.edu";
    const SESSION_URL = "https://scratch.mit.edu/session/";
    const MAX_STUDIOS = 35;

    document.getElementById(PANEL_ID)?.remove();

    if (location.hostname !== "scratch.mit.edu") {
        alert("Scratch (https://scratch.mit.edu) の作品ページで実行してください。");
        return;
    }

    const projectMatch = location.pathname.match(/^\/projects\/(\d+)(?:\/|$)/);
    if (!projectMatch) {
        alert("作品ページ（/projects/作品ID）で実行してください。");
        return;
    }
    const projectId = projectMatch[1];

    const readStudios = () => {
        try {
            const value = JSON.parse(localStorage.getItem(STORAGE_KEY) || "[]");
            if (!Array.isArray(value)) return [];
            return value
                .filter(item => item && /^\d+$/.test(String(item.id)))
                .map(item => ({
                    id: String(item.id),
                    title: String(item.title || `スタジオ ${item.id}`),
                    selected: Boolean(item.selected)
                }));
        } catch {
            return [];
        }
    };

    let studios = readStudios();
    let busy = false;

    const saveStudios = () => {
        localStorage.setItem(STORAGE_KEY, JSON.stringify(studios));
    };

    const host = document.createElement("div");
    host.id = PANEL_ID;
    const shadow = host.attachShadow({ mode: "open" });
    document.documentElement.appendChild(host);

    shadow.innerHTML = `
        <style>
            :host { all: initial; }
            * { box-sizing: border-box; }
            .panel {
                position: fixed; z-index: 2147483647; top: 84px; right: 16px;
                width: min(390px, calc(100vw - 32px)); max-height: calc(100vh - 104px);
                overflow: hidden; display: flex; flex-direction: column;
                color: #1f2937; background: #fff; border: 1px solid #d1d5db;
                border-radius: 12px; box-shadow: 0 12px 36px #0003;
                font: 14px/1.45 system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
            }
            header {
                display: flex; justify-content: space-between; align-items: center;
                padding: 12px 14px; color: #fff; background: #4c97ff; font-weight: 700;
            }
            header small { display: block; margin-top: 2px; font-size: 11px; font-weight: 500; opacity: .9; }
            button {
                border: 0; border-radius: 7px; padding: 8px 10px; cursor: pointer;
                color: #fff; background: #4c97ff; font: inherit; font-weight: 650;
            }
            button:hover { filter: brightness(.95); }
            button:disabled { cursor: wait; opacity: .55; }
            .close { padding: 4px 8px; color: #fff; background: #ffffff2b; font-size: 18px; }
            main { overflow: auto; padding: 13px; }
            .hint { margin: 0 0 10px; color: #4b5563; font-size: 12px; }
            textarea {
                display: block; width: 100%; min-height: 66px; resize: vertical;
                padding: 9px; border: 1px solid #cbd5e1; border-radius: 7px;
                color: #111827; background: #fff; font: inherit;
            }
            .row { display: flex; gap: 7px; margin-top: 8px; }
            .row > button { flex: 1; }
            .secondary { color: #374151; background: #e5e7eb; }
            .danger { color: #991b1b; background: #fee2e2; }
            .list-head { display: flex; justify-content: space-between; align-items: center; margin: 15px 0 6px; }
            .list-head strong { font-size: 13px; }
            .list-head button { padding: 4px 7px; color: #374151; background: #f3f4f6; font-size: 11px; }
            .list { display: grid; gap: 5px; max-height: 230px; overflow: auto; }
            .studio {
                display: grid; grid-template-columns: 20px 1fr auto; gap: 7px; align-items: center;
                padding: 7px; border: 1px solid #e5e7eb; border-radius: 7px;
            }
            .studio input { margin: 0; accent-color: #4c97ff; }
            .studio a { min-width: 0; overflow: hidden; color: #1d4ed8; text-decoration: none; text-overflow: ellipsis; white-space: nowrap; }
            .studio a:hover { text-decoration: underline; }
            .studio .remove { padding: 4px 7px; color: #991b1b; background: #fee2e2; font-size: 11px; }
            .empty { margin: 8px 0; color: #6b7280; font-size: 12px; }
            .add-selected { width: 100%; margin-top: 12px; padding: 10px; }
            .status {
                white-space: pre-wrap; margin: 10px 0 0; padding: 8px; border-radius: 7px;
                color: #374151; background: #f3f4f6; font-size: 12px;
            }
            .status:empty { display: none; }
            .status.error { color: #991b1b; background: #fef2f2; }
            .status.success { color: #166534; background: #f0fdf4; }
            .foot { margin: 10px 0 0; color: #6b7280; font-size: 11px; }
        </style>
        <section class="panel" role="dialog" aria-label="Scratchスタジオ一括追加">
            <header>
                <div>スタジオ一括追加<small>作品ID: ${projectId}</small></div>
                <button class="close" type="button" aria-label="パネルを閉じる">×</button>
            </header>
            <main>
                <p class="hint">スタジオIDまたはURLを改行・空白・カンマ区切りで入力できます。登録は最大35件です。</p>
                <textarea class="studio-input" placeholder="例:&#10;https://scratch.mit.edu/studios/12345678&#10;87654321"></textarea>
                <div class="row">
                    <button class="register" type="button">スタジオを登録</button>
                    <button class="select-all secondary" type="button">全選択</button>
                    <button class="clear-selection secondary" type="button">選択解除</button>
                </div>
                <div class="list-head"><strong>登録済みスタジオ</strong><span class="count"></span></div>
                <div class="list"></div>
                <button class="add-selected add-selected-button" type="button">選択したスタジオに追加</button>
                <div class="status" role="status" aria-live="polite"></div>
                <p class="foot">登録内容と選択状態はこのブラウザーに保存されます。ログイン情報は保存しません。</p>
            </main>
        </section>
    `;

    const $ = selector => shadow.querySelector(selector);
    const list = $(".list");
    const status = $(".status");
    const registerButton = $(".register");
    const addButton = $(".add-selected-button");
    const allButtons = [...shadow.querySelectorAll("button")];

    const setStatus = (message, kind = "") => {
        status.textContent = message;
        status.className = `status ${kind}`.trim();
    };

    const setBusy = value => {
        busy = value;
        allButtons.forEach(button => {
            if (button.classList.contains("close")) return;
            button.disabled = value;
        });
    };

    const render = () => {
        list.replaceChildren();
        $(".count").textContent = `${studios.length}/${MAX_STUDIOS}件`;

        if (studios.length === 0) {
            const empty = document.createElement("p");
            empty.className = "empty";
            empty.textContent = "まだ登録されていません。";
            list.appendChild(empty);
            return;
        }

        studios.forEach(studio => {
            const row = document.createElement("label");
            row.className = "studio";

            const checkbox = document.createElement("input");
            checkbox.type = "checkbox";
            checkbox.checked = studio.selected;
            checkbox.setAttribute("aria-label", `${studio.title}を選択`);
            checkbox.addEventListener("change", () => {
                studio.selected = checkbox.checked;
                saveStudios();
            });

            const link = document.createElement("a");
            link.href = `https://scratch.mit.edu/studios/${studio.id}`;
            link.target = "_blank";
            link.rel = "noopener noreferrer";
            link.textContent = studio.title;
            link.title = `${studio.title} (${studio.id})`;

            const remove = document.createElement("button");
            remove.type = "button";
            remove.className = "remove";
            remove.textContent = "削除";
            remove.setAttribute("aria-label", `${studio.title}を登録一覧から削除`);
            remove.addEventListener("click", event => {
                event.preventDefault();
                studios = studios.filter(item => item.id !== studio.id);
                saveStudios();
                render();
            });

            row.append(checkbox, link, remove);
            list.appendChild(row);
        });
    };

    const parseStudioId = value => {
        const text = value.trim();
        if (/^\d+$/.test(text)) return text;
        try {
            const url = new URL(text);
            if (!["scratch.mit.edu", "www.scratch.mit.edu"].includes(url.hostname)) return null;
            return url.pathname.match(/^\/studios\/(\d+)(?:\/|$)/)?.[1] || null;
        } catch {
            return text.match(/^(?:https?:\/\/)?(?:www\.)?scratch\.mit\.edu\/studios\/(\d+)(?:\/|$)/i)?.[1] || null;
        }
    };

    const getStudioTitle = async id => {
        const response = await fetch(`${API_BASE}/studios/${id}/`, { credentials: "omit" });
        if (!response.ok) throw new Error(`HTTP ${response.status}`);
        const studio = await response.json();
        return typeof studio.title === "string" && studio.title.trim()
            ? studio.title.trim()
            : `スタジオ ${id}`;
    };

    const getCsrfToken = () => {
        const item = document.cookie.split(";").map(part => part.trim())
            .find(part => part.startsWith("scratchcsrftoken="));
        if (!item) return "";
        return decodeURIComponent(item.slice("scratchcsrftoken=".length));
    };

    const getSessionToken = async () => {
        const response = await fetch(SESSION_URL, {
            method: "GET",
            credentials: "include",
            headers: { "X-Requested-With": "XMLHttpRequest" }
        });
        if (!response.ok) throw new Error(`ログイン状態を確認できませんでした (HTTP ${response.status})`);
        const session = await response.json();
        const user = session?.user;
        const token = user?.token || session?.token || user?.xToken;
        if (!user || !token) {
            throw new Error("Scratchにログインしていないか、セッションを取得できませんでした。ログイン後にもう一度実行してください。");
        }
        return { token, username: user.username || "ログイン中のユーザー" };
    };

    const sleep = ms => new Promise(resolve => setTimeout(resolve, ms));

    const registerStudios = async () => {
        if (busy) return;
        const values = $(".studio-input").value
            .split(/[\s,，]+/)
            .map(parseStudioId)
            .filter(Boolean);
        const uniqueIds = [...new Set(values)];
        const invalidCount = $(".studio-input").value
            .split(/[\s,，]+/)
            .filter(value => value.trim() && !parseStudioId(value)).length;

        if (uniqueIds.length === 0) {
            setStatus("スタジオIDまたはScratchのスタジオURLを入力してください。", "error");
            return;
        }
        const newIds = uniqueIds.filter(id => !studios.some(studio => studio.id === id));
        if (studios.length + newIds.length > MAX_STUDIOS) {
            setStatus(
                `登録できるスタジオは合計${MAX_STUDIOS}件までです。現在${studios.length}件登録されています。不要なスタジオを削除してから再度お試しください。`,
                "error"
            );
            return;
        }

        setBusy(true);
        setStatus("スタジオ情報を確認しています…");
        const added = [];
        const nameErrors = [];
        try {
            for (const id of newIds) {
                let title = `スタジオ ${id}`;
                try {
                    title = await getStudioTitle(id);
                } catch (error) {
                    nameErrors.push(`${id}: ${error.message}`);
                }
                studios.push({ id, title, selected: true });
                added.push(id);
            }
            saveStudios();
            render();
            const messages = [];
            if (added.length) messages.push(`${added.length}件を登録しました。`);
            else messages.push("入力されたスタジオはすべて登録済みです。");
            if (invalidCount) messages.push(`読み取れない入力を${invalidCount}件無視しました。`);
            if (nameErrors.length) messages.push("一部のスタジオ名を取得できず、ID表示にしています。");
            setStatus(messages.join("\n"), nameErrors.length ? "" : "success");
        } finally {
            setBusy(false);
        }
    };

    const addToSelectedStudios = async () => {
        if (busy) return;
        const targets = studios.filter(studio => studio.selected);
        if (!targets.length) {
            setStatus("追加先を1件以上選択してください。", "error");
            return;
        }

        const targetText = targets.map(studio => `・${studio.title} (${studio.id})`).join("\n");
        const approved = confirm(
            `作品 ${projectId} を次の${targets.length}件のスタジオに追加します。\n\n${targetText}\n\n続けますか？`
        );
        if (!approved) return;

        setBusy(true);
        setStatus("ログイン状態を確認しています…");
        try {
            const csrfToken = getCsrfToken();
            if (!csrfToken) {
                throw new Error("CSRFトークンのCookieを読み取れません。Scratchにログインし、作品ページを再読み込みしてください。");
            }
            const { token, username } = await getSessionToken();
            const successes = [];
            const failures = [];

            for (let index = 0; index < targets.length; index++) {
                const studio = targets[index];
                setStatus(`追加中 (${index + 1}/${targets.length})\n${studio.title}`);
                const url = new URL(`${API_BASE}/studios/${studio.id}/project/${projectId}`);
                url.searchParams.set("x-token", token);

                try {
                    const response = await fetch(url.toString(), {
                        method: "POST",
                        credentials: "include",
                        headers: {
                            "X-CSRFToken": csrfToken,
                            "X-Requested-With": "XMLHttpRequest"
                        }
                    });
                    if (response.ok) {
                        successes.push(studio);
                    } else {
                        const responseText = (await response.text()).slice(0, 240);
                        failures.push(`${studio.title} (${studio.id}): HTTP ${response.status}${responseText ? ` — ${responseText}` : ""}`);
                        if (response.status === 401 || response.status === 403 || response.status === 429) {
                            failures.push("認証・権限エラー、またはアクセス制限のため、残りの処理を停止しました。");
                            break;
                        }
                    }
                } catch (error) {
                    failures.push(`${studio.title} (${studio.id}): ${error.message || "通信エラー"}`);
                }
                if (index < targets.length - 1) await sleep(700);
            }

            const lines = [`実行ユーザー: ${username}`, `成功: ${successes.length}件`];
            if (successes.length) lines.push(...successes.map(studio => `✓ ${studio.title}`));
            if (failures.length) lines.push("", `失敗: ${failures.length}件`, ...failures.map(message => `✕ ${message}`));
            setStatus(lines.join("\n"), failures.length ? (successes.length ? "" : "error") : "success");
        } catch (error) {
            setStatus(error.message || String(error), "error");
        } finally {
            setBusy(false);
        }
    };

    registerButton.addEventListener("click", registerStudios);
    addButton.addEventListener("click", addToSelectedStudios);
    $(".select-all").addEventListener("click", () => {
        studios.forEach(studio => { studio.selected = true; });
        saveStudios();
        render();
    });
    $(".clear-selection").addEventListener("click", () => {
        studios.forEach(studio => { studio.selected = false; });
        saveStudios();
        render();
    });
    $(".close").addEventListener("click", () => host.remove());

    render();
    console.info(`[Scratch Studio Multi-Adder] 準備完了。作品ID: ${projectId}`);
})();
```
