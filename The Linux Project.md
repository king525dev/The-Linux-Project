---

---
# Summary: The Linux Project

> **Status:** Planning  
> **Type:** Project  
> **Created:** 2026-05-23

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
const currentFolder = currentFile.split("/").slice(0, -1).join("/");  
  
// get all files inside current folder  
const items = app.vault.getFiles()  
.filter(f => f.path.startsWith(currentFolder + "/"))  
.filter(f => f.path !== currentFile)  
.sort((a, b) => a.path.localeCompare(b.path));  
  
const container = dv.el("div", "");  
container.style.display = "grid";  
container.style.gap = "12px";  
  
// collect folders  
const folders = new Set();  
  
for (const file of items) {  
const relative = file.path.replace(currentFolder + "/", "");  
const parts = relative.split("/");  
  
if (parts.length > 1) {  
folders.add(parts[0]);  
}  
}  
  
// render folders  
for (const folder of [...folders].sort()) {  
  
const section = document.createElement("div");  
  
section.style.padding = "14px";  
section.style.borderRadius = "14px";  
section.style.background = "var(--background-secondary)";  
section.style.border = "1px solid var(--background-modifier-border)";  
  
section.innerHTML += `  
<div style="  
font-weight:700;  
margin-bottom:10px;  
font-size:1.05em;  
">  
📁 ${folder}  
</div>  
`;  
  
items  
.filter(f => {  
const relative = f.path.replace(currentFolder + "/", "");  
return relative.startsWith(folder + "/");  
})  
.forEach(f => {  
  
const link = document.createElement("a");  
  
link.href = f.path;  
link.className = "internal-link";  
link.textContent = f.basename;  
  
link.style.display = "block";  
link.style.padding = "6px 0";  
link.style.textDecoration = "none";  
link.style.color = "var(--text-normal)";  
  
section.appendChild(link);  
});  
  
container.appendChild(section);  
}  
  
// root-level files  
const rootFiles = items.filter(f => {  
const relative = f.path.replace(currentFolder + "/", "");  
return !relative.includes("/");  
});  
  
if (rootFiles.length) {  
  
const notes = document.createElement("div");  
  
notes.style.padding = "14px";  
notes.style.borderRadius = "14px";  
notes.style.background = "var(--background-secondary)";  
notes.style.border = "1px solid var(--background-modifier-border)";  
  
notes.innerHTML += `  
<div style="  
font-weight:700;  
margin-bottom:10px;  
font-size:1.05em;  
">  
📄 Notes  
</div>  
`;  
  
rootFiles.forEach(f => {  
  
const link = document.createElement("a");  
  
link.href = f.path;  
link.className = "internal-link";  
link.textContent = f.basename;  
  
link.style.display = "block";  
link.style.padding = "6px 0";  
link.style.textDecoration = "none";  
link.style.color = "var(--text-normal)";  
  
notes.appendChild(link);  
});  
  
container.appendChild(notes);  
}  
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

Ideas, discoveries, thoughts, scratch notes, etc.

