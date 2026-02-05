## PR Commits

```js
let commits = [];
let commitItemEls = document.querySelectorAll("[data-commit-link]");
for (let commitItemEl of commitItemEls) {
    let commit = {
        message: commitItemEl.querySelector("[class*=CommitRow-module__ListItemTitle_]")?.innerText.trim(),
        hash: commitItemEl.querySelectorAll("a")[3].href.match(/\/commits\/([a-z0-9]+)$/)?.[1] ?? null,
        datetime: commitItemEl.querySelector("relative-time").datetime,
        name: commitItemEl.querySelectorAll("a")[2].innerText.trim(),
    };
    commits.push(commit);
}
// console.log(commits);
// copy(JSON.stringify(commits, null, "    "));
function getColumn(array, columnName) {
    return array.map(item => item[columnName]);
}
// copy(getColumn(commits, "hash").join("\n"));
copy(getColumn(commits, "datetime").join("\n"));
```

## Git Log

```bash
git show \
  --no-patch \
  --pretty=format:"COMMIT|%H|%an|%ad|%s" \
  --date=iso \
  --numstat \
  $(cat hashes.txt) |
awk '
/^COMMIT/ {
  if (NR>1) print hash "|" author "|" date "|" msg "|" "+"ins " -"del
  split($0,a,"|")
  hash=a[2]; author=a[3]; date=a[4]; msg=a[5]
  ins=0; del=0
  next
}
{
  ins+=$1; del+=$2
}
END {
  print hash "|" author "|" date "|" msg "|" "+"ins " -"del
}'
```