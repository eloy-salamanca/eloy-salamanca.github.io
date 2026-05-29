# 🚀 Automating CHANGELOGs in GitLab CI — A Reusable and Scalable Approach

<!-- SEO: gitlab ci changelog automation, git-cliff tutorial, automated changelog devops -->

## 🧭 Introduction

Keeping a `CHANGELOG.md` up to date sounds simple — until you scale.

> ❌ We kept forgetting to update our CHANGELOG.md  
> ❌ It was inconsistent across repos  
> ❌ It didn’t scale  
>  
> So we fixed it properly.  
>  
> ✅ One GitLab pipeline  
> ✅ Automatic changelog generation  
> ✅ Centralised reuse  
> ✅ Zero manual effort  
>  
> Result?  
> 🚀 Clean, consistent changelogs everywhere  
> 🚀 Developers don’t even think about it anymore  
>  
> Biggest lesson:  
> 👉 CI/CD is not automation  
> 👉 It’s platform engineering  

---

## 🏗️ Architecture Overview

```
![Architecture Diagram](architecture_diagram.png)
```

---

## ⚙️ Step 1 — Centralised Pipeline

```yaml
include:
  - project: "org/shared-ci-pipelines"
    ref: "v1.0.0"
    file: "templates/module-ci.yml"
```

---

## 📄 Step 2 — git-cliff

Example header:

Changelog header uses a placeholder replaced at runtime.

```bash
sed -i "s/MODULE_NAME/${CI_PROJECT_NAME}/g" cliff.toml
```

---

## 🔁 Step 3 — Generate changelog

```bash
git-cliff --config cliff.toml --tag ${VERSION} -o CHANGELOG.md
```

---

## 🔐 Step 4 — Authentication

Use a Group Access Token exposed as:

PROJECT_ACCESS_TOKEN

---

## 🔒 Step 5 — Branch protection

Allow Maintainers to push.

---

## 🧪 Step 6 — Debugging

```bash
echo $PROJECT_ACCESS_TOKEN | wc -c
```

---

## 📊 Pipeline Flow

![Pipeline Flow](pipeline_flow.png)

---

## 📄 Example Generated CHANGELOG

Below is an example of the automatically generated `CHANGELOG.md`
following the Keep a Changelog format and Semantic Versioning:

![Example CHANGELOG](sample_changelog.png)

👉 Each entry is automatically derived from commit messages following the Conventional Commits standard.

## 🧠 Real-World Lessons Learned

- Tooling limitations matter
- GitLab variables can be tricky
- Debugging visibility is key
- Deterministic pipelines are better
- Centralisation scales

---

## 🎉 Conclusion

We automated changelogs across repositories using a single pipeline.

✅ No manual work
✅ Consistent output
✅ Fully reusable setup

Biggest lesson: CI/CD is platform engineering.

---

## 🙌 Final Thoughts

One include → full automation.

---

#devops #gitlab #automation #platformengineering