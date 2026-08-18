/*
  Subtitle Download Card — auto-render widget
  ---------------------------------------------
  1) Put an empty container where you want the cards, with your data on it:

     <div class="subtitle-cards" data-subs='[
       {"lang":"EN","file":"Movie.2026.1080p.WEB-DL.srt","uploader":"subrider88","synced":true,"quality":"WEB-DL","rating":9.2,"downloads":14800,"url":"https://example.com/en.srt"},
       {"lang":"FR","file":"Movie.2026.720p.BluRay.srt","uploader":"cine.traduit","synced":true,"quality":"BluRay","rating":8.7,"downloads":6100,"url":"https://example.com/fr.srt"}
     ]'></div>

  2) Then, right after it, one line:

     <script src="https://YOUR-HOST/subtitle-card.js"></script>

  ALTERNATIVE — pull data from a JSON URL instead of typing it inline:

     <div class="subtitle-cards" data-src="https://YOUR-HOST/subs.json"></div>
     <script src="https://YOUR-HOST/subtitle-card.js"></script>

  The JSON at that URL should just be an array of the same objects shown above.
*/
(function () {
  var STYLE_ID = "subtitle-card-styles";

  function injectStyles() {
    if (document.getElementById(STYLE_ID)) return;
    var css =
      '.sc-wrap{font-family:Inter,system-ui,sans-serif;width:100%;}' +
      '.sc-list{display:flex;flex-direction:column;gap:10px;}' +
      '.sc-card{background:#121212;border:1px solid #242422;border-radius:6px;display:flex;align-items:center;gap:18px;padding:14px 18px;transition:border-color .15s ease,background .15s ease;}' +
      '.sc-card:hover{background:#161616;border-color:#3a3a37;}' +
      '.sc-lang{flex:0 0 auto;font-weight:800;font-size:15px;letter-spacing:.03em;color:#f2f2ef;border:1.5px solid #f2f2ef;border-radius:3px;padding:6px 9px;line-height:1;min-width:52px;text-align:center;}' +
      '.sc-info{flex:1 1 auto;min-width:0;display:flex;flex-direction:column;gap:4px;}' +
      '.sc-file{font-family:"IBM Plex Mono",Menlo,Consolas,monospace;font-size:13px;color:#f2f2ef;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;}' +
      '.sc-byline{font-size:12.5px;color:#88867f;display:flex;align-items:center;gap:6px;}' +
      '.sc-byline .sc-name{color:#f2f2ef;font-weight:500;}' +
      '.sc-dot{width:3px;height:3px;border-radius:50%;background:#525049;flex:0 0 auto;}' +
      '.sc-sprockets{flex:0 0 auto;display:flex;flex-direction:column;gap:4px;padding:0 2px;}' +
      '.sc-sprockets span{width:3px;height:3px;border-radius:50%;background:#3a3a37;display:block;}' +
      '.sc-stats{flex:0 0 auto;display:flex;align-items:center;gap:18px;}' +
      '.sc-stat{display:flex;align-items:center;gap:5px;font-size:12.5px;color:#88867f;}' +
      '.sc-stat svg{width:14px;height:14px;stroke:#525049;flex:0 0 auto;}' +
      '.sc-stat.sc-rating{color:#f2f2ef;}' +
      '.sc-stat.sc-rating svg{stroke:#f2f2ef;}' +
      '.sc-dl{flex:0 0 auto;display:flex;align-items:center;gap:7px;background:#f2f2ef;color:#0a0a0a;border:none;border-radius:3px;padding:9px 16px;font-weight:800;font-size:11.5px;letter-spacing:.08em;text-transform:uppercase;text-decoration:none;cursor:pointer;transition:opacity .15s ease;}' +
      '.sc-dl:hover{opacity:.85;}' +
      '.sc-dl svg{width:13px;height:13px;}' +
      '@media (max-width:640px){.sc-card{flex-wrap:wrap;row-gap:12px;}.sc-sprockets{display:none;}.sc-stats{order:3;flex:1 1 100%;border-top:1px solid #242422;padding-top:10px;}.sc-dl{order:2;}}';
    var tag = document.createElement("style");
    tag.id = STYLE_ID;
    tag.textContent = css;
    document.head.appendChild(tag);
  }

  function esc(str) {
    return String(str == null ? "" : str).replace(/[&<>"']/g, function (c) {
      return { "&": "&amp;", "<": "&lt;", ">": "&gt;", '"': "&quot;", "'": "&#39;" }[c];
    });
  }

  function formatCount(n) {
    n = Number(n) || 0;
    if (n >= 1000) return (n / 1000).toFixed(1).replace(/\.0$/, "") + "k";
    return String(n);
  }

  var STAR =
    '<svg viewBox="0 0 24 24" fill="none" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polygon points="12 2 15 9 22 9.5 17 14.5 18.5 22 12 18 5.5 22 7 14.5 2 9.5 9 9 12 2"/></svg>';
  var DOWN_ARROW =
    '<svg viewBox="0 0 24 24" fill="none" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M12 5v13M6 12l6 6 6-6"/></svg>';
  var DL_ICON =
    '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.4" stroke-linecap="round" stroke-linejoin="round"><path d="M12 4v11M7 11l5 5 5-5"/><path d="M5 19h14"/></svg>';

  function cardHtml(sub) {
    var lang = esc(sub.lang || "?");
    var file = esc(sub.file || sub.filename || "unknown.srt");
    var uploader = esc(sub.uploader || sub.owner || "anonymous");
    var synced = sub.synced === false ? "unsynced" : "synced";
    var quality = esc(sub.quality || "");
    var rating = sub.rating != null ? Number(sub.rating).toFixed(1) : null;
    var downloads = sub.downloads != null ? formatCount(sub.downloads) : null;
    var url = sub.url ? esc(sub.url) : "#";

    var statsHtml = "";
    if (rating != null) {
      statsHtml += '<div class="sc-stat sc-rating">' + STAR + rating + "</div>";
    }
    if (downloads != null) {
      statsHtml += '<div class="sc-stat">' + DOWN_ARROW + downloads + "</div>";
    }

    return (
      '<div class="sc-card">' +
        '<div class="sc-lang">[' + lang + ']</div>' +
        '<div class="sc-info">' +
          '<div class="sc-file">' + file + "</div>" +
          '<div class="sc-byline"><span class="sc-name">' + uploader + "</span>" +
            '<span class="sc-dot"></span><span>' + synced + (quality ? " &middot; " + quality : "") + "</span>" +
          "</div>" +
        "</div>" +
        '<div class="sc-sprockets"><span></span><span></span><span></span></div>' +
        '<div class="sc-stats">' + statsHtml + "</div>" +
        '<a class="sc-dl" href="' + url + '" download>' + DL_ICON + "Download</a>" +
      "</div>"
    );
  }

  function render(container, subs) {
    injectStyles();
    if (!Array.isArray(subs) || subs.length === 0) {
      container.innerHTML = '<div class="sc-wrap"><p style="color:#88867f;font-size:13px;">No subtitles found.</p></div>';
      return;
    }
    var html = subs.map(cardHtml).join("");
    container.innerHTML = '<div class="sc-wrap"><div class="sc-list">' + html + "</div></div>";
  }

  function initContainer(el) {
    var inline = el.getAttribute("data-subs");
    var src = el.getAttribute("data-src");

    if (inline) {
      try {
        render(el, JSON.parse(inline));
      } catch (e) {
        el.innerHTML = '<p style="color:#88867f;font-size:13px;">Subtitle data could not be read.</p>';
      }
      return;
    }

    if (src) {
      injectStyles();
      el.innerHTML = '<div class="sc-wrap"><p style="color:#88867f;font-size:13px;">Loading subtitles…</p></div>';
      fetch(src)
        .then(function (res) { return res.json(); })
        .then(function (data) { render(el, data); })
        .catch(function () {
          el.innerHTML = '<p style="color:#88867f;font-size:13px;">Could not load subtitle list.</p>';
        });
      return;
    }

    el.innerHTML = '<p style="color:#88867f;font-size:13px;">Add data-subs or data-src to this element.</p>';
  }

  function init() {
    var containers = document.querySelectorAll(".subtitle-cards");
    for (var i = 0; i < containers.length; i++) initContainer(containers[i]);
  }

  if (document.readyState === "loading") {
    document.addEventListener("DOMContentLoaded", init);
  } else {
    init();
  }

  // Manual API, in case you want to render into a container from your own code:
  // SubtitleCards.render(document.getElementById('my-div'), [ {...}, {...} ]);
  window.SubtitleCards = { render: render, init: init };
})();
