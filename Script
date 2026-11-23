/* Rubens Monteiro — script.js (JavaScript puro, sem dependências)
   Ordem: 1) utilitários  2) dados  3) preenchimento  4) revelação  5) menu  6) cópia
          7) imagens  8) demonstração  9) início */
(() => {
  'use strict';

  /* ===== 1. Utilitários (todo texto entra na página com textContent, nunca innerHTML) ===== */
  const $ = (s, r = document) => r.querySelector(s);
  const $$ = (s, r = document) => [...r.querySelectorAll(s)];
  const txt = v => (typeof v === 'string' || typeof v === 'number' ? String(v).trim() : '');
  const get = (o, p) => p.split('.').reduce((a, k) => (a == null ? a : a[k]), o);
  const el = (tag, cls, text) => { const e = document.createElement(tag); if (cls) e.className = cls; if (text) e.textContent = text; return e; };
  const soNum = s => s.replace(/\D/g, '');
  const emailOk = s => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(s);
  const LISTAS = ['formacao', 'certificacoes', 'experiencia', 'cadastros'];

  /* ===== 2. Dados =====
     PADRAO é o fallback seguro: vale se o dados.json não carregar ou se o campo vier vazio/inválido.
     Se mudar o contato no painel, o site passa a usar o valor do painel. */
  const PADRAO = {
    nome: 'Rubens Monteiro',
    titulo: 'Perito Grafotécnico e Assistente Técnico Extrajudicial',
    whatsapp: '5521964177283',
    telefone: '+55 21 96417-7283',
    email: 'perito.rubensmonteiro@gmail.com'
  };

  function normalizar(d) {
    d = d && typeof d === 'object' ? d : {};
    const p = d.perito || {}, c = d.contatos || {};
    const itens = a => (Array.isArray(a) ? a : [])
      .map(i => ({ titulo: txt(i && i.titulo), instituicao: txt(i && i.instituicao), periodo: txt(i && i.periodo), observacao: txt(i && i.observacao) }))
      .filter(i => i.titulo || i.instituicao || i.periodo || i.observacao);
    const out = { perito: {}, contatos: {} };
    ['foto', 'foto_alt', 'resumo', 'biografia', 'trajetoria', 'preparacao_tecnica', 'proposito', 'compromisso_etico'].forEach(k => { out.perito[k] = txt(p[k]); });
    out.perito.nome = txt(p.nome) || PADRAO.nome;
    out.perito.titulo = txt(p.titulo) || PADRAO.titulo;
    LISTAS.forEach(k => { out.perito[k] = itens(p[k]); });
    out.contatos.whatsapp = soNum(txt(c.whatsapp)).length >= 10 ? soNum(txt(c.whatsapp)) : PADRAO.whatsapp;
    out.contatos.telefone = soNum(txt(c.telefone)).length >= 10 ? txt(c.telefone) : PADRAO.telefone;
    out.contatos.email = emailOk(txt(c.email)) ? txt(c.email) : PADRAO.email;
    out.contatos.localidade = txt(c.localidade);
    out.servicos = (Array.isArray(d.servicos) ? d.servicos : [])
      .map(s => ({ nome: txt(s && s.nome), descricao: txt(s && s.descricao), indicado_para: txt(s && s.indicado_para) }))
      .filter(s => s.nome || s.descricao);
    return out;
  }

  let dados = normalizar({});

  /* ===== 3. Preenchimento da página ===== */
  function paragrafos(box, t) {
    box.replaceChildren();
    t.split(/\n\s*\n/).map(s => s.trim()).filter(Boolean).forEach(s => box.append(el('p', '', s)));
  }

  function lista(box, itens) {
    box.replaceChildren();
    itens.forEach(i => {
      const li = el('li');
      if (i.titulo) li.append(el('strong', '', i.titulo));
      const meta = [i.instituicao, i.periodo].filter(Boolean).join(' — ');
      if (meta) li.append(el('em', '', meta));
      if (i.observacao) li.append(el('p', '', i.observacao));
      box.append(li);
    });
  }

  function preencher(d) {
    // Campos simples: data-campo="grupo.campo". Vazio => elemento oculto.
    $$('[data-campo]').forEach(n => {
      const v = get(d, n.dataset.campo);
      const t = typeof v === 'string' ? v : '';
      n.textContent = t;
      n.hidden = !t;
    });
    $$('.hero__lead').forEach(n => { n.hidden = !!d.perito.resumo; });

    // Trajetória: bloco vazio some; sem nenhum bloco, somem a seção e os links do menu.
    $$('[data-bloco]').forEach(b => {
      const v = get(d, b.dataset.bloco);
      const box = $('[data-texto],[data-lista]', b);
      if (Array.isArray(v)) lista(box, v); else paragrafos(box, v || '');
      b.hidden = !(Array.isArray(v) ? v.length : v);
    });
    const sobre = $('#sobre');
    const temSobre = $$('[data-bloco]', sobre).some(b => !b.hidden);
    sobre.hidden = !temSobre;
    $$('[data-nav="sobre"]').forEach(a => { a.hidden = !temSobre; });

    // Fotografia do perito: aceita apenas caminhos dentro de /uploads. Sem ela, vale a foto editorial.
    const f = d.perito.foto;
    const okFoto = /^\/?uploads\/[\w\-./%]+$/i.test(f) && !f.includes('..');
    const src = f.startsWith('/') ? f : '/' + f;
    $$('[data-foto]').forEach(i => {
      i.hidden = !okFoto;
      if (okFoto) { i.src = src; i.alt = d.perito.foto_alt || 'Fotografia de ' + d.perito.nome; }
    });
    $$('[data-ilus="hero"]').forEach(i => { i.hidden = okFoto; });
    $$('.sobre__fig').forEach(n => { n.hidden = !okFoto; });

    // Serviços.
    const ol = $('[data-servicos]');
    ol.replaceChildren();
    d.servicos.forEach((s, i) => {
      const li = el('li', 'ficha revelar');
      li.style.setProperty('--i', i % 3);
      li.append(el('h3', '', s.nome), el('p', '', s.descricao));
      if (s.indicado_para) {
        const p = el('p', 'ficha__para');
        p.append(el('strong', '', 'Indicado para: '), document.createTextNode(s.indicado_para));
        li.append(p);
      }
      ol.append(li);
    });
    $('#servicos').hidden = !d.servicos.length;
    $$('[data-nav="servicos"]').forEach(a => { a.hidden = !d.servicos.length; });

    // Links de contato (WhatsApp, telefone e e-mail), sempre válidos graças ao fallback.
    const c = d.contatos, tel = c.telefone.replace(/(?!^\+)\D/g, '');
    $$('[data-wa]').forEach(a => { a.href = 'https://wa.me/' + c.whatsapp; a.target = '_blank'; a.rel = 'noopener noreferrer'; });
    $$('[data-tel]').forEach(a => { a.href = 'tel:' + tel; });
    $$('[data-mail]').forEach(a => { a.href = 'mailto:' + c.email; });

    // Dados estruturados (Schema.org Person), montados a partir dos dados; sem endereço.
    const ld = { '@context': 'https://schema.org', '@type': 'Person', name: d.perito.nome, jobTitle: d.perito.titulo, email: c.email, telephone: tel };
    if (okFoto) ld.image = location.origin + src;
    const s = el('script');
    s.type = 'application/ld+json';
    s.textContent = JSON.stringify(ld);
    document.head.append(s);
  }

  /* ===== 4. Revelação suave ao rolar (Intersection Observer) ===== */
  let io = null;
  function revelar() {
    const itens = $$('.revelar:not(.visivel)');
    if (!('IntersectionObserver' in window) || matchMedia('(prefers-reduced-motion: reduce)').matches) {
      itens.forEach(n => n.classList.add('visivel'));
      return;
    }
    io = io || new IntersectionObserver(es => es.forEach(e => {
      if (e.isIntersecting) { e.target.classList.add('visivel'); io.unobserve(e.target); }
    }), { threshold: 0.12, rootMargin: '0px 0px -6% 0px' });
    itens.forEach(n => io.observe(n));
  }

  /* ===== 5. Menu móvel e cabeçalho ===== */
  function menu() {
    const b = $('.menu-btn'), m = $('#menu'), topo = $('#topo');
    const fechar = () => { b.setAttribute('aria-expanded', 'false'); m.classList.remove('aberto'); };
    b.addEventListener('click', () => {
      const abrir = b.getAttribute('aria-expanded') !== 'true';
      b.setAttribute('aria-expanded', String(abrir));
      m.classList.toggle('aberto', abrir);
    });
    m.addEventListener('click', e => { if (e.target.closest('a')) fechar(); });
    document.addEventListener('keydown', e => { if (e.key === 'Escape') fechar(); });
    const rolagem = () => topo.classList.toggle('rolou', window.scrollY > 8);
    rolagem();
    window.addEventListener('scroll', rolagem, { passive: true });
  }

  /* ===== 6. Copiar e-mail e telefone ===== */
  function copiar(btn, valor) {
    const ok = () => {
      const rot = btn.dataset.rot || (btn.dataset.rot = btn.textContent);
      btn.textContent = 'Copiado!';
      clearTimeout(btn._t);
      btn._t = setTimeout(() => { btn.textContent = rot; }, 1800);
    };
    const reserva = () => {
      const t = el('textarea');
      t.value = valor;
      t.setAttribute('readonly', '');
      t.style.position = 'fixed';
      t.style.opacity = '0';
      document.body.append(t);
      t.select();
      t.setSelectionRange(0, valor.length);
      try { if (document.execCommand('copy')) ok(); } catch (e) { /* sem suporte: o texto continua selecionável */ }
      t.remove();
    };
    if (navigator.clipboard && window.isSecureContext) navigator.clipboard.writeText(valor).then(ok, reserva); else reserva();
  }

  /* ===== 7. Imagens: se uma foto externa falhar, o bloco some sem deixar ícone quebrado ===== */
  function imagens() {
    $$('img[data-ilus]').forEach(i => {
      const falhou = () => {
        i.hidden = true;
        const f = i.closest('figure');
        if (f && !$$('img', f).some(x => !x.hidden)) f.hidden = true;
      };
      if (i.complete && !i.naturalWidth && i.getAttribute('src')) falhou(); else i.addEventListener('error', falhou, { once: true });
    });
  }

  /* ===== 8. Demonstração técnica (traços sintéticos; didática, não é exame pericial) ===== */
  const CAR = {
    ataque: ['Ataque', 'Como o traço começa: o ponto de partida e os primeiros milímetros do movimento. Os círculos marcam o início de cada segmento.'],
    remate: ['Remate', 'Como o traço termina: se é seco, prolongado, recolhido ou em gancho. Os círculos marcam o fim de cada segmento.'],
    calibre: ['Calibre', 'Dimensão dos grafismos (altura e largura) e sua constância ao longo da escrita. Use o controle para variar o tamanho da amostra.'],
    pressao: ['Pressão gráfica', 'Força aplicada ao instrumento escritor; pode deixar marcas no suporte e variações de intensidade. Em imagem digital, a espessura ou a cor do traço, sozinha, não permite concluir sobre pressão: o exame depende do original, do instrumento e do suporte.'],
    inclinacao: ['Inclinação axial', 'Ângulo do eixo das letras em relação à linha de base. Mova o controle e observe o eixo tracejado.'],
    momentos: ['Momentos gráficos', 'Trechos executados sem levantar o instrumento escritor. Os números indicam a ordem dos três segmentos desta amostra.']
  };

  function bancada() {
    const sv = $('#amostra');
    if (!sv) return;
    const g = $('#grafia'), b = $('#amostra-b'), eixo = $('#eixo'), ex = $('#explica');
    const ri = $('#r-inc'), rc = $('#r-cal'), rb = $('#r-b'), lupa = $('#lupa'), palco = $('#palco');
    let inc = 0, esc = 1;

    const aplicar = () => {
      g.setAttribute('transform', `translate(160 112) skewX(${-inc}) scale(${esc}) translate(-160 -112)`);
      eixo.setAttribute('transform', `rotate(${inc} 160 112)`);
    };
    ri.addEventListener('input', () => { inc = Number(ri.value); aplicar(); });
    rc.addEventListener('input', () => { esc = Number(rc.value) / 100; aplicar(); });
    rb.addEventListener('change', () => { b.classList.toggle('on', rb.checked); });

    $$('[data-car]').forEach(btn => btn.addEventListener('click', () => {
      const k = btn.dataset.car;
      $$('[data-car]').forEach(x => x.setAttribute('aria-pressed', String(x === btn)));
      sv.setAttribute('class', 'm-' + k);
      ex.replaceChildren(el('strong', '', CAR[k][0] + '. '), document.createTextNode(CAR[k][1]));
      ri.parentElement.hidden = k !== 'inclinacao';
      rc.parentElement.hidden = k !== 'calibre';
    }));

    // Lupa: mouse (passar), toque (tocar e arrastar) e teclado (setas).
    const mover = (cx, cy) => {
      const r = sv.getBoundingClientRect(), p = palco.getBoundingClientRect();
      const x = (cx - r.left) / r.width * 320, y = (cy - r.top) / r.height * 140;
      lupa.firstElementChild.setAttribute('viewBox', `${x - 18} ${y - 18} 36 36`);
      lupa.style.left = (cx - p.left) + 'px';
      lupa.style.top = (cy - p.top) + 'px';
      lupa.hidden = false;
    };
    const esconder = () => { lupa.hidden = true; };
    palco.addEventListener('pointerdown', e => mover(e.clientX, e.clientY));
    palco.addEventListener('pointermove', e => { if (e.pointerType === 'mouse' || !lupa.hidden) mover(e.clientX, e.clientY); });
    ['pointerup', 'pointercancel', 'pointerleave', 'blur'].forEach(n => palco.addEventListener(n, esconder));
    let kx = 160, ky = 70;
    palco.addEventListener('keydown', e => {
      const d = { ArrowLeft: [-10, 0], ArrowRight: [10, 0], ArrowUp: [0, -10], ArrowDown: [0, 10] }[e.key];
      if (!d) return;
      e.preventDefault();
      kx = Math.max(0, Math.min(320, kx + d[0]));
      ky = Math.max(0, Math.min(140, ky + d[1]));
      const r = sv.getBoundingClientRect();
      mover(r.left + kx / 320 * r.width, r.top + ky / 140 * r.height);
    });
  }

  /* ===== 9. Início: uma falha isolada não derruba a página ===== */
  async function iniciar() {
    const seguro = f => { try { f(); } catch (e) { console.error(e); } };
    revelar();
    seguro(menu);
    seguro(bancada);
    seguro(imagens);
    $$('[data-copiar]').forEach(btn => btn.addEventListener('click', () => copiar(btn, dados.contatos[btn.dataset.copiar] || '')));
    $('#ano').textContent = new Date().getFullYear();
    try {
      const r = await fetch('dados.json', { cache: 'no-cache' });
      if (!r.ok) throw new Error('HTTP ' + r.status);
      dados = normalizar(await r.json());
      preencher(dados);
    } catch (e) {
      console.error('Não foi possível carregar o dados.json:', e);
      dados = normalizar({});
      seguro(() => preencher(dados));
      $$('[data-erro]').forEach(n => { n.hidden = false; });
    }
    revelar();
  }
  iniciar();
})();
