# Sidekick Bookmarklet

A [bookmarklet](https://en.wikipedia.org/wiki/Bookmarklet) (or favlet) is a bookmark stored in your browser. It uses JavaScript to add additional features. The Sidekick bookmarklet gives authors a toolbar with context-aware actions like Preview or Publish.

---
id: form
---

<label for="giturl">Repository URL:</label>
<input id="giturl" placeholder="https://github.com/....">
<label for="host">Production Hostname (optional): </label>
<input id="host">
<label for="project">Project Name (optional): </label>
<input id="project">
<br>
<button onclick="run()">Generate Bookmarklet</button>

---
id: book
style: display:none
---

Drag the Helix logo below to your browser's bookmark bar, or <a href="#" onclick="copy()">copy</a> its <b>Link Address</b> to add the bookmarklet manually.

<a id="bookmark" title="Sidekick" href="">
  <img src="./helix.svg" alt="Sidekick">
</a>

<style>

#bookmark {
  color: transparent;
  margin: 40px auto;
  display: block;
  width: 100px;
  height: 100px;
  padding: 20px;
  box-shadow: 0 0 10px 5px rgba(0, 0, 0, 0.2);
  border-radius: 50px;
}

@media (prefers-color-scheme: dark) {
  #bookmark {
    box-shadow: 0 0 10px 5px rgba(255, 255, 255, 0.2);
  }
}

</style>

<script>
  function copy() {
    const text = document.getElementById('bookmark').href;
    navigator.clipboard.writeText(text);
  }

  function run() {
    let giturl = document.getElementById('giturl').value;
    const host = document.getElementById('host').value;
    // const title = document.getElementById('title').value;
    const project = document.getElementById('project').value;
    if (!giturl) {
      alert('Repository URL is mandatory.');
      return;
    }
    giturl = new URL(giturl);
    const segs = giturl.pathname.substring(1).split('/');
    const owner = segs[0];
    const repo = segs[1];
    const ref = segs[3] || 'main';

    const config = {
      project,
      host,
      owner,
      repo,
      ref,
    };

    const bm=document.getElementById('bookmark');
    bm.href = [
      'javascript:',
      '/* ** Helix Sidekick Bookmarklet ** */',
      '(() => {if(!window.hlxSidekick){',
        `window.hlxSidekickConfig=${JSON.stringify(config)};`,
        'document.head.appendChild(document.createElement("script")).src="https://www.hlx.page/tools/sidekick/app.js";',
      '}else{window.hlxSidekick.toggle();}',
      '})();',
    ].join('');
    if (project) {
      const title = `${project} Sidekick`;
      bm.setAttribute('title', title);
      bm.firstElementChild.setAttribute('alt', title);
    }
    document.getElementById('book').style.display = 'block';
  }

  function init() {
    let autorun = false;
    new URLSearchParams(window.location.search).forEach((v,k) => {
      const field = document.getElementById(k);
      if (!field) return;
      field.value = v;
      autorun = true;
    });
    if (autorun) {
      document.getElementById('form').style.display = 'none';
      run();
    }
  }

  init();
</script>
