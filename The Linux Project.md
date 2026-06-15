---

---
# Summary: The Linux Project

> **Status:** Planning  
> **Type:** Project  
> **Created:** 2026-05-23

[[Projects Home]]

## Objective 

Set-up a three node local network that can host projects and perform services for me.

# Backlog

``` dataviewjs  
const pages = dv.pages(`"${dv.current().file.folder}/Sprints"`)

// columns
const columns = {
    pending: [],
    active: [],
    done: []
};

// collect sprint pages
for (let p of pages) {
	const path = p.file.path;

    // try to detect sprint files by name OR folder
    const isSprint =
        path.toLowerCase().includes("sprint");

    if (isSprint) {
        const status = (p.status ?? "pending").toLowerCase();

	    if (!columns[status]) {
	        columns.pending.push(p); // fallback safety
	    } else {
	        columns[status].push(p);
	    }

    }
}

// card renderer
function createCard(p) {
    const card = document.createElement("a");
    const isOverdue = p.end && dv.date(p.end) < dv.date("today") && p.status !== "done";
    
    const now = dv.date("now");  
	const dueDate = p.end ? dv.date(p.end) : null;  
	  
	const hoursLeft = dueDate ? dueDate.diff(now, "hours").hours : null;

	const isDone = (p.status ?? "").toLowerCase() === "done";

    card.href = p.file.path;
    card.className = "internal-link";

    // 🔧 critical reset for Obsidian + anchor behavior
    card.style.display = "block";
    card.style.textDecoration = "none";
    card.style.color = "inherit";

    // card layout
    card.style.padding = "12px";
    card.style.marginBottom = "10px";
    card.style.border = "1px solid var(--background-modifier-border)";
    card.style.borderRadius = "12px";
    card.style.background = "var(--background-secondary)";
    card.style.cursor = "pointer";
    
    // about to be over due
    if (p.end && dv.date(p.end) > dv.date("now") && p.status !== "done" && !isOverdue && dv.date(p.end).diff(dv.date("now"), "hours").hours <= 24) card.style.border = "1px solid #ffb020";

    // smooth hover feel
    card.style.transition = "all 0.15s ease";
    
    card.onmouseenter = () => {
        card.style.transform = "translateY(-2px)";
        card.style.background = "var(--background-modifier-hover)";
        card.style.borderColor = "var(--interactive-accent)";
    };

    card.onmouseleave = () => {
        card.style.transform = "translateY(0)";
        card.style.background = "var(--background-secondary)";
        card.style.borderColor = "var(--background-modifier-border)";
    };
    
    if (!isDone && dueDate) {  
  
		// 🔴 overdue  
		if (dueDate < now) {  
		card.style.border = "1px solid #ff4d4d";  
		card.style.boxShadow = "0 0 8px rgba(255, 80, 80, 0.35)";  
		}  
		  
		// 🟠 due within 24h  
		else if (hoursLeft <= 24) {  
		card.style.border = "1px solid #ffb020";  
		card.style.boxShadow = "0 0 8px rgba(255, 176, 32, 0.35)";  
		}  
	}
    
    if (isOverdue) {
	    card.style.border = "1px solid #ff4d4d";
	    card.style.boxShadow = "0 0 8px rgba(255, 80, 80, 0.35)";
	}

    const title = p.file.name;
	const project = p.project ?? "Unknown Project";     
	const due = p.end ? dv.date(p.end).toFormat("dd-MM-yy") : "No due date";

    card.innerHTML = `
        <div style="
            font-size: 1.05em;
            font-weight: 700;
            margin-bottom: 6px;
        ">
            ${title}
        </div>

        <div style="font-size: 0.85em; opacity: 0.75; margin-bottom: 2px;">
            📁 ${project}
        </div>
        
        <div style="font-size: 0.85em; opacity: 0.8;"> 📅 ${due} </div>
    `;

    return card;
}

// column renderer
function column(title, items) {
    const col = document.createElement("div");

    col.style.padding = "8px";

    col.innerHTML = `
        <div style="font-weight:700; margin-bottom:10px;">
            ${title.toUpperCase()}
        </div>
    `;

    if (items.length === 0) {
        const empty = document.createElement("div");
        empty.innerHTML = `<i style="opacity:0.6;">No items</i>`;
        col.appendChild(empty);
        return col;
    }

    for (const p of items) {
        col.appendChild(createCard(p));
    }

    return col;
}

// board
const board = document.createElement("div");

board.style.display = "grid";
board.style.gridTemplateColumns = "repeat(3, 1fr)";
board.style.gap = "12px";

board.appendChild(column("🟡 Pending", columns.pending));
board.appendChild(column("🟢 Active", columns.active));
board.appendChild(column("⚫ Done", columns.done));

dv.container.appendChild(board);
```


## Directory

```dataviewjs  
const currentFile = dv.current().file.path;

const currentFolder = currentFile
    .split("/")
    .slice(0, -1)
    .join("/");

// CONFIG
const ignoreFolders = [
    "docsVenv",
    ".obsidian",
    "overrides"
];

const ignoreFiles = [
    "Project Home"
];

// get files
const items = app.vault.getFiles()
    .filter(f => f.path.startsWith(currentFolder + "/"))
    .filter(f => f.path !== currentFile)
    .sort((a, b) => a.path.localeCompare(b.path));

// container
const container = dv.el("div", "");

container.style.display = "flex";
container.style.flexDirection = "column";
container.style.gap = "14px";

// collect folders
const folders = new Set();

for (const file of items) {

    const relative =
        file.path.replace(currentFolder + "/", "");

    const parts = relative.split("/");

    if (parts.length > 1) {

        const folder = parts[0];

        if (!ignoreFolders.includes(folder)) {
            folders.add(folder);
        }
    }
}

// helper: create links
function createLink(file) {

    const link = document.createElement("a");

    link.href = file.path;

    link.className = "internal-link";

    link.style.display = "flex";
    link.style.alignItems = "center";
    link.style.gap = "10px";

    link.style.padding = "10px 12px";

    link.style.borderRadius = "10px";

    link.style.marginBottom = "6px";

    link.style.textDecoration = "none";

    link.style.color = "var(--text-normal)";

    link.style.transition = "all 0.12s ease";

    link.innerHTML = `
        <span style="opacity:0.8;">📄</span>
        <span>${file.basename}</span>
    `;

    link.onmouseenter = () => {

        link.style.background =
            "var(--background-modifier-hover)";
    };

    link.onmouseleave = () => {

        link.style.background = "transparent";
    };

    return link;
}

// accordion renderer
function createAccordion(title, icon, files) {

    const details = document.createElement("details");

    details.style.border =
        "1px solid var(--background-modifier-border)";

    details.style.borderRadius = "14px";

    details.style.background =
        "var(--background-secondary)";

    details.style.padding = "4px 10px";

    details.style.transition = "all 0.15s ease";

    // sprint metadata
    const sprintFiles = files.filter(f => {

        const cache =
            app.metadataCache.getFileCache(f);

        return cache?.frontmatter?.type === "sprint";
    });

    const activeSprint =
        sprintFiles.some(f => {

            const cache =
                app.metadataCache.getFileCache(f);

            return cache?.frontmatter?.active === true;
        });

    // auto-open active folders
    if (activeSprint) {
        details.open = true;
    }

    // status badge
    let badge = "⚫";

    if (activeSprint) {
        badge = "🟢";
    }
    else if (sprintFiles.length > 0) {
        badge = "🟡";
    }

    details.onmouseenter = () => {

        details.style.borderColor =
            "var(--interactive-accent)";
    };

    details.onmouseleave = () => {

        details.style.borderColor =
            "var(--background-modifier-border)";
    };

    // summary
    const summary = document.createElement("summary");

    summary.style.cursor = "pointer";

    summary.style.listStyle = "none";

    summary.style.padding = "14px 6px";

    summary.style.fontWeight = "700";

    summary.style.fontSize = "1.05em";

    summary.style.display = "flex";

    summary.style.justifyContent = "space-between";

    summary.style.alignItems = "center";

    const activeIndicator =
        activeSprint ? "🔥" : "";

    summary.innerHTML = `
    <div style="
        display:flex;
        align-items:center;
        gap:10px;
    ">

        <span>
            ${title}
        </span>

        <span style="
            opacity:0.65;
            font-size:0.9em;
        ">
            (${files.length})
        </span>
    </div>

    <div style="
        display:flex;
        align-items:center;
        gap:8px;
    ">
        <span>${activeIndicator}</span>
        <span>${badge}</span>
    </div>
    `;

    details.appendChild(summary);

    // content
    const content = document.createElement("div");

    content.style.padding =
        "8px 2px 12px 2px";

    files.forEach(f => {

        content.appendChild(createLink(f));
    });

    details.appendChild(content);

    return details;
}

// render folders
for (const folder of [...folders].sort()) {

    const folderFiles = items

        .filter(f => {

            const relative =
                f.path.replace(currentFolder + "/", "");

            return relative.startsWith(folder + "/");
        })

        .filter(f =>
            !ignoreFiles.includes(f.basename)
        );

    if (folderFiles.length > 0) {

        container.appendChild(
            createAccordion(
                folder,
                "📁",
                folderFiles
            )
        );
    }
}

// ROOT NOTES FIX
const rootFiles = items

    .filter(f => {

        const relative =
            f.path.replace(currentFolder + "/", "");

        return !relative.includes("/");
    })

    .filter(f =>
        !ignoreFiles.includes(f.basename)
    );

// render root notes
if (rootFiles.length > 0) {

    container.appendChild(
        createAccordion(
            "Notes",
            "📝",
            rootFiles
        )
    );
}

// render final container
dv.container.appendChild(container);
```




## Resources/Links
- [FUTO Self Managed Guide](https://wiki.futo.org/index.php/Introduction_to_a_Self_Managed_Life:_a_13_hour_%26_28_minute_presentation_by_FUTO_software)

## Metadata

| Property | Value |
| -------- | ----- |
| Category |       |
| Priority |       |
| Deadline |       |

# Notes

- **Budget:** [[Linux Project Expenses.usheet]]

