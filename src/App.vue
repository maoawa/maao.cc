<script setup>
import { computed, onBeforeUnmount, onMounted, ref, watchEffect } from 'vue';
import { useI18n } from 'vue-i18n';
import {
  faBilibili,
  faDiscord,
  faGithub,
  faInstagram,
  faQq,
  faSteam,
  faTelegram,
  faTiktok,
  faWeixin,
  faXTwitter,
} from '@fortawesome/free-brands-svg-icons';
import {
  faBlog,
  faCameraRetro,
  faChevronRight,
  faEnvelope,
  faGlobe,
  faLanguage,
  faMessage,
  faPhone,
  faQrcode,
  faTerminal,
} from '@fortawesome/free-solid-svg-icons';

const { locale, t, tm } = useI18n();
const showQr = ref(false);

const languages = [
  { code: 'en', label: 'English' },
  { code: 'zh', label: '简体中文' },
  { code: 'ja', label: '日本語' },
  { code: 'it', label: 'Italiano' },
  { code: 'es', label: 'Español' },
];

const contactValues = {
  discord: 'winmemzqwq',
  x: '@winmemzqwq',
  telegram: '@maoawa',
  email: '',
  imessage: '',
  github: 'maoawa',
  instagram: '@winmemzqwq',
  tiktok: '@maoawa',
  steam: '@winmemzqwq',
  wechat: 'paimon-kawaii',
  qq: '1874985948',
  bilibili: 'WinMEMZ',
  phone: '',
  phoneUs: '',
  phoneUk: '',
};

const protectedTargets = {
  email: {
    scheme: ['bWFp', 'bHRv'],
    value: ['d2lu', 'bWVt', 'enF3', 'cUBn', 'bWFp', 'bC5j', 'b20='],
    display: ['d2lu', 'bWVt', 'enF3', 'cUBn', 'bWFp', 'bC5j', 'b20='],
  },
  imessage: {
    scheme: ['c21z'],
    value: ['bWVu', 'Z3hp', 'bnF3', 'cUBp', 'Y2xv', 'dWQu', 'Y29t'],
    display: ['bWVu', 'Z3hp', 'bnF3', 'cUBp', 'Y2xv', 'dWQu', 'Y29t'],
  },
  phone: {
    scheme: ['dGVs'],
    value: ['Kzg2', 'MTk5', 'ODU0', 'MDg4', 'MTg='],
    display: ['Kzg2', 'IDE5', 'OSA4', 'NTQw', 'IDg4', 'MTg='],
  },
  phoneUs: {
    scheme: ['dGVs'],
    value: ['KzEy', 'MTMz', 'MjA2', 'ODg4'],
    display: ['KzEg', 'KDIx', 'Mykg', 'MzIw', 'LTY4', 'ODg='],
  },
  phoneUk: {
    scheme: ['dGVs'],
    value: ['KzQ0', 'Nzcx', 'OTc3', 'NDg1', 'NA=='],
    display: ['KzQ0', 'IDc3', 'MTkg', 'Nzc0', 'ODU0'],
  },
};

const primaryContacts = [
  {
    key: 'discord',
    icon: faDiscord,
    href: 'https://discord.com/users/742704239410675725',
  },
  {
    key: 'x',
    icon: faXTwitter,
    href: 'https://twitter.com/winmemzqwq',
  },
  {
    key: 'telegram',
    icon: faTelegram,
    href: 'https://t.me/maoawa',
  },
  {
    key: 'email',
    icon: faEnvelope,
    protected: 'email',
  },
];

const otherContacts = [
  {
    key: 'imessage',
    icon: faMessage,
    protected: 'imessage',
  },
  { key: 'github', icon: faGithub, href: 'https://github.com/maoawa' },
  { key: 'instagram', icon: faInstagram, href: 'https://www.instagram.com/winmemzqwq' },
  { key: 'tiktok', icon: faTiktok, href: 'https://www.tiktok.com/@maaoawa' },
  { key: 'steam', icon: faSteam, href: 'https://steamcommunity.com/id/WinMEMZ' },
  {
    key: 'phoneUs',
    icon: faPhone,
    protected: 'phoneUs',
  },
  {
    key: 'phoneUk',
    icon: faPhone,
    protected: 'phoneUk',
  },
];

const mainlandContacts = [
  { key: 'wechat', icon: faWeixin, href: null, qr: true },
  { key: 'qq', icon: faQq, href: 'tencent://message/?uin=1874985948' },
  { key: 'bilibili', icon: faBilibili, href: 'https://space.bilibili.com/417031122' },
  {
    key: 'phone',
    icon: faPhone,
    protected: 'phone',
  },
];

const projects = [
  { key: 'vrchat', icon: faCameraRetro, href: 'https://vrchat.maao.cc/', urlLabel: 'vrchat.maao.cc' },
  { key: 'enBlog', icon: faGlobe, href: 'https://marsinside.com/', urlLabel: 'marsinside.com' },
  { key: 'cnBlog', icon: faBlog, href: 'https://www.maodream.com/', urlLabel: 'maodream.com' },
];

const terminalLines = computed(() => tm('terminalLines'));
const titleLines = computed(() => tm('introLines'));
const isChinese = computed(() => locale.value === 'zh');

watchEffect(() => {
  document.documentElement.lang = locale.value === 'ja' ? 'ja' : locale.value;
  document.title = t('metaTitle');
});

function selectLocale(code) {
  locale.value = code;
  localStorage.setItem('preferredLanguage', code);
}

function openQr() {
  showQr.value = true;
}

function closeQr() {
  showQr.value = false;
}

function handleKeydown(event) {
  if (event.key === 'Escape') closeQr();
}

onMounted(() => {
  window.addEventListener('keydown', handleKeydown);
});

onBeforeUnmount(() => {
  window.removeEventListener('keydown', handleKeydown);
});

function labelFor(item, field) {
  return t(`contacts.${item.key}.${field}`);
}

function displayValue(item) {
  if (item.protected) return obfuscateDisplay(decodeParts(protectedTargets[item.protected].display));
  return contactValues[item.key] || '';
}

function decodeParts(parts) {
  return atob(parts.join(''));
}

function obfuscateDisplay(value) {
  const invisibleJoiner = '\u2060';
  return Array.from(value).join(invisibleJoiner);
}

function openProtectedContact(contact) {
  const target = protectedTargets[contact.protected];
  if (!target) return;
  const scheme = decodeParts(target.scheme);
  const value = decodeParts(target.value);
  window.location.href = `${scheme}:${value}`;
}

function iconViewBox(icon) {
  return `0 0 ${icon.icon[0]} ${icon.icon[1]}`;
}

function iconPaths(icon) {
  const data = icon.icon[4];
  return Array.isArray(data) ? data : [data];
}
</script>

<template>
  <main class="site-shell">
    <div class="ambient-grid" aria-hidden="true"></div>

    <header class="topbar">
      <a class="brand" href="/" aria-label="maao.cc">
        <span class="brand-mark">
          <svg :viewBox="iconViewBox(faTerminal)" aria-hidden="true">
            <path v-for="path in iconPaths(faTerminal)" :key="path" :d="path" />
          </svg>
        </span>
        <span>maao.cc</span>
      </a>

      <label class="language-picker">
        <svg :viewBox="iconViewBox(faLanguage)" aria-hidden="true">
          <path v-for="path in iconPaths(faLanguage)" :key="path" :d="path" />
        </svg>
        <span class="sr-only">{{ t('chooseLanguage') }}</span>
        <select :value="locale" :aria-label="t('chooseLanguage')" @change="selectLocale($event.target.value)">
          <option v-for="language in languages" :key="language.code" :value="language.code">
            {{ language.label }}
          </option>
        </select>
      </label>
    </header>

    <section class="hero">
      <div class="hero-copy">
        <p class="eyebrow">{{ t('eyebrow') }}</p>
        <h1 class="rainbow-title">
          <span v-for="line in titleLines" :key="line">{{ line }}</span>
        </h1>
        <p class="intro-line">
          {{ t('introLineBefore') }} <code>maoawa</code>{{ t('introLineAfter') }}
        </p>
        <div class="status-line">
          <span class="status-dot" aria-hidden="true"></span>
          <span>{{ t('availability') }}</span>
        </div>
      </div>

      <div class="terminal-panel" :aria-label="t('terminalCaption')">
        <div class="terminal-header">
          <span></span>
          <span></span>
          <span></span>
        </div>
        <div class="terminal-body">
          <p v-for="line in terminalLines" :key="line">
            <span>mars@maao.cc</span> {{ line }}
          </p>
          <img src="/assets/paimon.png" alt="" loading="lazy" />
        </div>
      </div>
    </section>

    <section v-if="isChinese" class="contact-section compact" aria-labelledby="mainland-contact-title-first">
      <div class="section-heading">
        <p class="eyebrow">cn.routes</p>
        <h2 id="mainland-contact-title-first">{{ t('mainlandTitle') }}</h2>
      </div>

      <div class="compact-grid">
        <component
          :is="contact.qr || contact.protected ? 'button' : 'a'"
          v-for="contact in mainlandContacts"
          :key="contact.key"
          class="contact-card compact-card"
          :href="contact.qr || contact.protected ? undefined : contact.href"
          :target="contact.qr || contact.protected ? undefined : '_blank'"
          :rel="contact.qr || contact.protected ? undefined : 'noopener noreferrer'"
          :type="contact.qr || contact.protected ? 'button' : undefined"
          @click="contact.qr ? openQr() : contact.protected ? openProtectedContact(contact) : undefined"
        >
          <span class="contact-icon">
            <svg :viewBox="iconViewBox(contact.icon)" aria-hidden="true">
              <path v-for="path in iconPaths(contact.icon)" :key="path" :d="path" />
            </svg>
          </span>
          <span class="contact-copy">
            <strong>{{ labelFor(contact, 'label') }}</strong>
            <code>{{ displayValue(contact) }}</code>
            <small v-if="contact.protected" class="tap-hint">{{ t('protectedHint') }}</small>
          </span>
          <svg class="contact-arrow" :viewBox="iconViewBox(contact.qr ? faQrcode : faChevronRight)" aria-hidden="true">
            <path v-for="path in iconPaths(contact.qr ? faQrcode : faChevronRight)" :key="path" :d="path" />
          </svg>
        </component>
      </div>

    </section>

    <section class="contact-section" aria-labelledby="primary-contact-title">
      <div class="section-heading">
        <p class="eyebrow">{{ t('primarySubtitle') }}</p>
        <h2 id="primary-contact-title">{{ t('primaryTitle') }}</h2>
      </div>

      <div class="primary-grid">
        <component
          :is="contact.protected ? 'button' : 'a'"
          v-for="contact in primaryContacts"
          :key="contact.key"
          class="contact-card primary-card"
          :href="contact.protected ? undefined : contact.href"
          :target="contact.protected ? undefined : '_blank'"
          :rel="contact.protected ? undefined : 'noopener noreferrer'"
          :type="contact.protected ? 'button' : undefined"
          @click="contact.protected ? openProtectedContact(contact) : undefined"
        >
          <span class="contact-icon">
            <svg :viewBox="iconViewBox(contact.icon)" aria-hidden="true">
              <path v-for="path in iconPaths(contact.icon)" :key="path" :d="path" />
            </svg>
          </span>
          <span class="contact-copy">
            <strong>{{ labelFor(contact, 'label') }}</strong>
            <small>{{ labelFor(contact, 'detail') }}</small>
            <code>{{ displayValue(contact) }}</code>
            <small v-if="contact.protected" class="tap-hint">{{ t('protectedHint') }}</small>
          </span>
          <svg class="contact-arrow" :viewBox="iconViewBox(faChevronRight)" aria-hidden="true">
            <path v-for="path in iconPaths(faChevronRight)" :key="path" :d="path" />
          </svg>
        </component>
      </div>

      <div class="subsection-heading">
        <p class="eyebrow">network.map</p>
        <h3>{{ t('moreTitle') }}</h3>
      </div>

      <div class="compact-grid">
        <component
          :is="contact.protected ? 'button' : 'a'"
          v-for="contact in otherContacts"
          :key="contact.key"
          class="contact-card compact-card"
          :href="contact.protected ? undefined : contact.href"
          :target="contact.protected ? undefined : '_blank'"
          :rel="contact.protected ? undefined : 'noopener noreferrer'"
          :type="contact.protected ? 'button' : undefined"
          @click="contact.protected ? openProtectedContact(contact) : undefined"
        >
          <span class="contact-icon">
            <svg :viewBox="iconViewBox(contact.icon)" aria-hidden="true">
              <path v-for="path in iconPaths(contact.icon)" :key="path" :d="path" />
            </svg>
          </span>
          <span class="contact-copy">
            <strong>{{ labelFor(contact, 'label') }}</strong>
            <code>{{ displayValue(contact) }}</code>
            <small v-if="contact.protected" class="tap-hint">{{ t('protectedHint') }}</small>
          </span>
          <svg :viewBox="iconViewBox(faChevronRight)" aria-hidden="true">
            <path v-for="path in iconPaths(faChevronRight)" :key="path" :d="path" />
          </svg>
        </component>
      </div>
    </section>

    <section class="info-band" aria-labelledby="about-title">
      <div>
        <p class="eyebrow">whoami</p>
        <h2 id="about-title">{{ t('aboutTitle') }}</h2>
        <p>{{ t('aboutBody') }}</p>
      </div>
    </section>

    <section class="contact-section compact" aria-labelledby="projects-title">
      <div class="section-heading">
        <p class="eyebrow">{{ t('projectsSubtitle') }}</p>
        <h2 id="projects-title">{{ t('projectsTitle') }}</h2>
      </div>

      <div class="project-grid">
        <a
          v-for="project in projects"
          :key="project.key"
          class="contact-card project-card"
          :href="project.href"
          target="_blank"
          rel="noopener noreferrer"
        >
          <span class="contact-icon">
            <svg :viewBox="iconViewBox(project.icon)" aria-hidden="true">
              <path v-for="path in iconPaths(project.icon)" :key="path" :d="path" />
            </svg>
          </span>
          <span class="contact-copy">
            <strong>{{ t(`projects.${project.key}.label`) }}</strong>
            <small>{{ t(`projects.${project.key}.detail`) }}</small>
            <code>{{ project.urlLabel }}</code>
          </span>
          <svg :viewBox="iconViewBox(faChevronRight)" aria-hidden="true">
            <path v-for="path in iconPaths(faChevronRight)" :key="path" :d="path" />
          </svg>
        </a>
      </div>
    </section>

    <section v-if="!isChinese" class="contact-section compact" aria-labelledby="mainland-contact-title">
      <div class="section-heading">
        <p class="eyebrow">cn.routes</p>
        <h2 id="mainland-contact-title">{{ t('mainlandTitle') }}</h2>
      </div>

      <div class="compact-grid">
        <component
          :is="contact.qr || contact.protected ? 'button' : 'a'"
          v-for="contact in mainlandContacts"
          :key="contact.key"
          class="contact-card compact-card"
          :href="contact.qr || contact.protected ? undefined : contact.href"
          :target="contact.qr || contact.protected ? undefined : '_blank'"
          :rel="contact.qr || contact.protected ? undefined : 'noopener noreferrer'"
          :type="contact.qr || contact.protected ? 'button' : undefined"
          @click="contact.qr ? openQr() : contact.protected ? openProtectedContact(contact) : undefined"
        >
          <span class="contact-icon">
            <svg :viewBox="iconViewBox(contact.icon)" aria-hidden="true">
              <path v-for="path in iconPaths(contact.icon)" :key="path" :d="path" />
            </svg>
          </span>
          <span class="contact-copy">
            <strong>{{ labelFor(contact, 'label') }}</strong>
            <code>{{ displayValue(contact) }}</code>
            <small v-if="contact.protected" class="tap-hint">{{ t('protectedHint') }}</small>
          </span>
          <svg class="contact-arrow" :viewBox="iconViewBox(contact.qr ? faQrcode : faChevronRight)" aria-hidden="true">
            <path v-for="path in iconPaths(contact.qr ? faQrcode : faChevronRight)" :key="path" :d="path" />
          </svg>
        </component>
      </div>

    </section>

    <div v-if="showQr" class="qr-modal" role="dialog" aria-modal="true" :aria-label="t('qrAlt')" @click.self="closeQr">
      <div class="qr-dialog">
        <button class="qr-close" type="button" :aria-label="t('hideQr')" @click="closeQr">×</button>
        <img src="/assets/wechat.jpg" :alt="t('qrAlt')" />
      </div>
    </div>

    <footer class="site-footer">
      <p>
        {{ t('footerRepo') }}
        <a href="https://github.com/maoawa/maao.cc" target="_blank" rel="noopener noreferrer">github.com/maoawa/maao.cc</a>
      </p>
      <p class="footer-links">
        <a href="https://icp.gov.moe/?keyword=20235320" target="_blank" rel="noopener noreferrer">{{ t('legal') }}</a>
        <span aria-hidden="true">·</span>
        <a href="https://im.maao.cc/" target="_blank" rel="noopener noreferrer">{{ t('footerMirror') }}</a>
      </p>
      <p>{{ t('footerUpdated') }}</p>
    </footer>

  </main>
</template>
