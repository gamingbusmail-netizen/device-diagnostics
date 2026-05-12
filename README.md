<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Device Diagnostics Center</title>
<meta name="viewport" content="width=device-width, initial-scale=1">
<style>
body { margin:0; background:#0b0d12; color:#e5e7eb; font-family:system-ui,-apple-system,BlinkMacSystemFont,"Segoe UI",sans-serif; }
header { padding:20px; border-bottom:1px solid #1f2937; }
h1 { margin:0; font-size:22px; text-transform:uppercase; letter-spacing:2px; }
#session { font-size:12px; color:#9ca3af; margin-top:4px; }
main { max-width:1100px; margin:20px auto; padding:0 16px; display:grid; gap:20px; grid-template-columns:2fr 1fr; }
.card { background:#111827; border:1px solid #1f2937; border-radius:12px; padding:16px; box-shadow:0 18px 40px rgba(0,0,0,0.4); }
.card h2 { margin:0 0 10px; font-size:14px; text-transform:uppercase; color:#9ca3af; letter-spacing:0.12em; }
.metric { margin-bottom:10px; }
.metric span { display:block; }
.label { font-size:11px; color:#9ca3af; text-transform:uppercase; letter-spacing:0.08em; }
.value { font-size:17px; font-weight:600; white-space: pre-wrap; }
button { background:#1f2937; border:1px solid #374151; color:#e5e7eb; padding:8px 14px; border-radius:999px; cursor:pointer; margin:4px 6px 4px 0; font-size:12px; }
button:hover { background:#374151; }
.log-box { max-height:380px; overflow-y:auto; font-size:12px; padding-right:4px; }
.log-line { border-bottom:1px solid #1f2937; padding:6px 0; }
.status-ok { color:#22c55e; }
.status-warn { color:#f59e0b; }
.status-bad { color:#ef4444; }
.badge { display:inline-block; padding:3px 8px; border-radius:999px; border:1px solid #374151; font-size:11px; color:#9ca3af; margin-right:6px; }
@media (max-width:800px){ main{ grid-template-columns:1fr; } }
</style>
</head>
<body>

<header>
  <h1>Device Diagnostics Center</h1>
  <div id="session"></div>
</header>

<main>
  <!-- LEFT SIDE: DEVICE + TESTS -->
  <section class="card">
    <h2>Device profile</h2>
    <div class="metric"><span class="label">Device</span><span class="value" id="dev-name">–</span></div>
    <div class="metric"><span class="label">Type</span><span class="value" id="dev-type">–</span></div>
    <div class="metric"><span class="label">OS</span><span class="value" id="dev-os">–</span></div>
    <div class="metric"><span class="label">Browser</span><span class="value" id="dev-browser">–</span></div>
    <div class="metric"><span class="label">Browser Engine</span><span class="value" id="dev-engine">–</span></div>
    <div class="metric"><span class="label">Environment</span><span class="value" id="dev-env">–</span></div>
    <div class="metric"><span class="label">Standalone / App</span><span class="value" id="dev-standalone">–</span></div>
    <div class="metric"><span class="label">Touch Support</span><span class="value" id="dev-touch">–</span></div>
    <div class="metric"><span class="label">RAM</span><span class="value" id="dev-ram">–</span></div>
    <div class="metric"><span class="label">CPU Threads</span><span class="value" id="dev-cpu">–</span></div>
    <div class="metric"><span class="label">Battery (raw)</span><span class="value" id="dev-battery">–</span></div>
    <div class="metric"><span class="label">Connection</span><span class="value" id="dev-conn">–</span></div>
    <div class="metric"><span class="label">Screen</span><span class="value" id="dev-screen">–</span></div>
    <div class="metric"><span class="label">Orientation</span><span class="value" id="dev-orientation">–</span></div>
    <div class="metric"><span class="label">WebGL</span><span class="value" id="dev-webgl">–</span></div>
    <div class="metric"><span class="label">Media support</span><span class="value" id="dev-media">–</span></div>
    <div class="metric"><span class="label">Clipboard support</span><span class="value" id="dev-clipboard">–</span></div>

    <div style="margin:10px 0;">
      <span class="badge" id="badge-brand"></span>
      <span class="badge" id="badge-tests"></span>
    </div>

    <h2 style="margin-top:18px;">Device search</h2>
    <div style="display:flex;flex-wrap:wrap;gap:8px;align-items:center;margin-bottom:10px;">
      <input id="device-search-input" placeholder="Search by brand, model, or OS" style="flex:1;min-width:220px;padding:8px 12px;border:1px solid #374151;border-radius:999px;background:#111827;color:#e5e7eb;outline:none;" />
      <button onclick="searchDevices()">Search</button>
      <button onclick="clearDeviceSearch()">Clear</button>
    </div>
    <div class="metric"><span class="label">Search results</span><span class="value" id="device-search-results">None</span></div>

    <h2 style="margin-top:18px;">Diagnostics</h2>
    <div>
      <button onclick="runAutoDiagnostics()">Run full auto diagnostics</button>
      <button id="background-btn" onclick="toggleBackgroundMode()">Start Background Diagnostics</button>
      <button onclick="sendDeveloperReport()">Send report to developer</button>
    </div>
    <div style="margin-top:10px;">
      <button onclick="runPing()">Ping</button>
      <button onclick="runCpu()">CPU</button>
      <button onclick="runBattery()">Battery</button>
      <button onclick="runSensors()">Sensors</button>
      <button onclick="runStorageCheck()">Storage</button>
      <button onclick="runMemoryCheck()">Memory</button>
      <button onclick="runTouchscreenCheck()">Touchscreen</button>
      <button onclick="runOrientationCheck()">Orientation</button>
      <button onclick="runWebGLCheck()">WebGL</button>
      <button onclick="runMediaSupportCheck()">Media</button>
      <button onclick="runClipboardCheck()">Clipboard</button>
    </div>

    <div style="margin-top:14px;">
      <div class="metric"><span class="label">Ping</span><span class="value" id="ping-val">–</span></div>
      <div class="metric"><span class="label">CPU Load</span><span class="value" id="cpu-val">–</span></div>
      <div class="metric"><span class="label">Battery Health</span><span class="value" id="battery-val">–</span></div>
      <div class="metric"><span class="label">Sensors</span><span class="value" id="sensor-val">–</span></div>
      <div class="metric"><span class="label">Storage</span><span class="value" id="storage-val">–</span></div>
      <div class="metric"><span class="label">Memory Check</span><span class="value" id="memory-val">–</span></div>
      <div class="metric"><span class="label">Thermal (sim)</span><span class="value" id="thermal-val">–</span></div>
      <div class="metric"><span class="label">Touch Test (sim)</span><span class="value" id="touch-val">–</span></div>
    </div>
  </section>

  <!-- RIGHT SIDE: LOGS -->
  <section class="card">
    <h2>Diagnostics log</h2>
    <div class="log-box" id="log"></div>
  </section>
</main>

<script>
// ---------- LOGGING ----------
function log(msg) {
  const box = document.getElementById("log");
  const line = document.createElement("div");
  line.className = "log-line";
  line.textContent = new Date().toLocaleTimeString() + " — " + msg;
  box.prepend(line);
}

document.getElementById("session").textContent =
  "Session: DC-" + Math.random().toString(36).substring(2,8).toUpperCase();

// ---------- DEVICE DETECTION ENGINE ----------
function detectDevice() {
  const ua = navigator.userAgent.toLowerCase();
  const platform = (navigator.platform || "").toLowerCase();

  let device = {
    brand: "Unknown",
    model: "Unknown",
    type: "Unknown",
    os: "Unknown"
  };

  // Apple
  if (ua.includes("iphone")) {
    device.brand = "Apple";
    device.type = "Phone";
    device.os = "iOS";
    device.model = "iPhone";
  } else if (ua.includes("ipad")) {
    device.brand = "Apple";
    device.type = "Tablet";
    device.os = "iPadOS";
    device.model = "iPad";
  } else if (ua.includes("macintosh") || ua.includes("mac os")) {
    device.brand = "Apple";
    device.type = "Computer";
    device.os = "macOS";
    device.model = "Mac";
  }

  // Samsung
  if (ua.includes("samsung") || ua.includes("sm-")) {
    device.brand = "Samsung";
    device.type = ua.includes("tablet") || ua.includes("sm-t") ? "Tablet" : "Phone";
    device.os = "Android";
    const m = ua.match(/sm-[a-z0-9]+/i);
    device.model = m ? m[0].toUpperCase() : "Galaxy Device";
  }

  // Pixel
  if (ua.includes("pixel")) {
    device.brand = "Google";
    device.type = ua.includes("tablet") ? "Tablet" : "Phone";
    device.os = ua.includes("android") ? "Android" : "Unknown";
    device.model = ua.includes("pixel tablet") ? "Pixel Tablet" : "Pixel";
  }

  // OnePlus
  if (ua.includes("oneplus")) {
    device.brand = "OnePlus";
    device.type = ua.includes("tablet") ? "Tablet" : "Phone";
    device.os = "Android";
    device.model = "OnePlus";
  }

  // Xiaomi / Redmi / Poco
  if (/\b(mi|redmi|poco)\b/.test(ua)) {
    device.brand = /redmi/.test(ua) ? "Redmi" : /poco/.test(ua) ? "Poco" : "Xiaomi";
    device.type = ua.includes("tablet") ? "Tablet" : "Phone";
    device.os = "Android";
    device.model = device.brand + " Device";
  }

  // Huawei / Honor
  if (ua.includes("huawei") || ua.includes("honor")) {
    device.brand = ua.includes("honor") ? "Honor" : "Huawei";
    device.type = ua.includes("tablet") ? "Tablet" : "Phone";
    device.os = "Android";
    device.model = device.brand + " Device";
  }

  // Motorola
  if (ua.includes("moto") || ua.includes("motorola")) {
    device.brand = "Motorola";
    device.type = ua.includes("tablet") ? "Tablet" : "Phone";
    device.os = "Android";
    device.model = ua.includes("moto") ? "Motorola" : "Motorola Device";
  }

  // Sony Xperia
  if (ua.includes("xperia") || ua.includes("sony")) {
    device.brand = "Sony";
    device.type = ua.includes("tablet") ? "Tablet" : "Phone";
    device.os = ua.includes("android") ? "Android" : "Unknown";
    device.model = "Xperia";
  }

  // LG
  if (ua.includes("lg-") || ua.includes("lg/")) {
    device.brand = "LG";
    device.type = ua.includes("tablet") ? "Tablet" : "Phone";
    device.os = "Android";
    device.model = "LG Device";
  }

  // Nokia
  if (ua.includes("nokia")) {
    device.brand = "Nokia";
    device.type = ua.includes("tablet") ? "Tablet" : "Phone";
    device.os = ua.includes("android") ? "Android" : "Unknown";
    device.model = "Nokia Device";
  }

  // Asus
  if (ua.includes("asus")) {
    device.brand = "Asus";
    device.type = ua.includes("tablet") ? "Tablet" : "Phone";
    device.os = ua.includes("android") ? "Android" : "Unknown";
    device.model = "Asus Device";
  }

  // Oppo / Vivo
  if (ua.includes("oppo") || ua.includes("vivo")) {
    device.brand = ua.includes("oppo") ? "Oppo" : "Vivo";
    device.type = ua.includes("tablet") ? "Tablet" : "Phone";
    device.os = "Android";
    device.model = device.brand + " Device";
  }

  // Amazon Fire / Kindle
  if (ua.includes("silk") || ua.includes("kf")) {
    device.brand = "Amazon";
    device.type = ua.includes("tablet") ? "Tablet" : "Phone";
    device.os = "Fire OS";
    device.model = "Fire Device";
  }

  // Windows
  if (ua.includes("windows")) {
    device.brand = "Microsoft";
    device.type = "Computer";
    device.os = "Windows";
    device.model = "Windows PC";
  }

  // Chromebook
  if (ua.includes("cros")) {
    device.brand = "Google";
    device.type = "Laptop";
    device.os = "ChromeOS";
    device.model = "Chromebook";
  }

  // Generic Android
  if (ua.includes("android") && device.brand === "Unknown") {
    device.brand = "Android";
    device.type = ua.includes("tablet") ? "Tablet" : "Phone";
    device.os = "Android";
    device.model = "Android Device";
  }

  // Fallback type from platform
  if (device.type === "Unknown") {
    if (platform.includes("win") || platform.includes("mac") || platform.includes("linux")) {
      device.type = "Computer";
    }
  }

  const standalone = window.matchMedia && window.matchMedia("(display-mode: standalone)").matches;
  const isElectron = typeof process === "object" && process.versions && !!process.versions.electron;
  const isCordova = !!window.cordova || !!window.Capacitor || /cordova|capacitor|ionic/i.test(ua);
  const isPWA = standalone || window.navigator.standalone === true;
  const hasTouch = navigator.maxTouchPoints > 0 || 'ontouchstart' in window;

  device.browserEngine = ua.includes("webkit") ? "WebKit/Blink" : ua.includes("gecko") ? "Gecko" : ua.includes("trident") || ua.includes("edgehtml") ? "EdgeHTML" : "Unknown";
  device.environment = isElectron ? "Electron" : isCordova ? "Cordova/Capacitor" : isPWA ? "PWA/Web App" : "Web Browser";
  device.isStandalone = standalone || isPWA;
  device.hasTouch = hasTouch;
  device.supportsWebGL = (() => {
    try {
      const canvas = document.createElement('canvas');
      return !!(window.WebGLRenderingContext && (canvas.getContext('webgl') || canvas.getContext('experimental-webgl')));
    } catch {
      return false;
    }
  })();

  return device;
}

const knownDevices = [
  { brand: "Apple", model: "iPhone", type: "Phone", os: "iOS", keywords: ["iphone", "ios", "apple"] },
  { brand: "Apple", model: "iPad", type: "Tablet", os: "iPadOS", keywords: ["ipad", "apple"] },
  { brand: "Apple", model: "Mac", type: "Computer", os: "macOS", keywords: ["mac", "macos", "apple"] },
  { brand: "Samsung", model: "Galaxy", type: "Phone", os: "Android", keywords: ["samsung", "galaxy", "android"] },
  { brand: "Samsung", model: "Galaxy Tablet", type: "Tablet", os: "Android", keywords: ["samsung", "tablet", "android"] },
  { brand: "Google", model: "Pixel", type: "Phone", os: "Android", keywords: ["pixel", "google", "android"] },
  { brand: "OnePlus", model: "OnePlus", type: "Phone", os: "Android", keywords: ["oneplus", "android"] },
  { brand: "Xiaomi", model: "Mi/Redmi/Poco", type: "Phone", os: "Android", keywords: ["xiaomi", "redmi", "poco", "android"] },
  { brand: "Huawei", model: "Huawei/Honor", type: "Phone", os: "Android", keywords: ["huawei", "honor", "android"] },
  { brand: "Motorola", model: "Moto", type: "Phone", os: "Android", keywords: ["motorola", "moto", "android"] },
  { brand: "Sony", model: "Xperia", type: "Phone", os: "Android", keywords: ["sony", "xperia", "android"] },
  { brand: "Amazon", model: "Fire", type: "Tablet", os: "Fire OS", keywords: ["amazon", "fire", "kindle"] },
  { brand: "Google", model: "Chromebook", type: "Laptop", os: "ChromeOS", keywords: ["chromebook", "chromeos", "cros"] },
  { brand: "Microsoft", model: "Windows PC", type: "Computer", os: "Windows", keywords: ["windows", "microsoft"] },
  { brand: "LG", model: "LG Device", type: "Phone", os: "Android", keywords: ["lg", "android"] },
  { brand: "Nokia", model: "Nokia Device", type: "Phone", os: "Android", keywords: ["nokia", "android"] },
  { brand: "Asus", model: "Asus Device", type: "Phone", os: "Android", keywords: ["asus", "android"] },
  { brand: "Oppo", model: "Oppo Device", type: "Phone", os: "Android", keywords: ["oppo", "android"] },
  { brand: "Vivo", model: "Vivo Device", type: "Phone", os: "Android", keywords: ["vivo", "android"] },
];

// ---------- TEST ROUTER ----------
function getTestsForDevice(device) {
  const tests = [];

  if (device.type === "Phone" || device.type === "Tablet") {
    tests.push("battery", "sensors", "cpu", "network", "storage", "touchscreen", "orientation");
  }

  if (device.type === "Computer" || device.type === "Laptop") {
    tests.push("cpu", "network", "storage", "memory", "webgl", "clipboard");
  }

  if (device.brand === "Apple") {
    tests.push("thermal");
  }

  if (device.brand === "Samsung") {
    tests.push("battery-health", "touch-test");
  }

  if (device.environment === "Electron" || device.environment === "Cordova/Capacitor") {
    tests.push("media", "orientation");
  }

  if (device.supportsWebGL) {
    tests.push("webgl");
  }

  return Array.from(new Set(tests));
}

const developerReportConfig = {
  webhookUrl: "", // Set your developer endpoint here for webhook-based delivery
  emails: ["developer@example.com"], // Add developer email addresses here
  autoSend: false,
};

// ---------- BASIC ENVIRONMENT POPULATION ----------
async function initEnvironment() {
  const device = detectDevice();
  const tests = getTestsForDevice(device);

  document.getElementById("dev-name").textContent = `${device.brand} ${device.model}`;
  document.getElementById("dev-type").textContent = device.type;
  document.getElementById("dev-os").textContent = device.os;
  document.getElementById("dev-browser").textContent = navigator.userAgent;
  document.getElementById("dev-engine").textContent = device.browserEngine || "Unknown";
  document.getElementById("dev-env").textContent = device.environment || "Unknown";
  document.getElementById("dev-standalone").textContent = device.isStandalone ? "Yes" : "No";
  document.getElementById("dev-touch").textContent = device.hasTouch ? "Yes" : "No";
  document.getElementById("dev-ram").textContent = (navigator.deviceMemory || "?") + " GB";
  document.getElementById("dev-cpu").textContent = navigator.hardwareConcurrency || "?";
  document.getElementById("dev-screen").textContent = `${screen.width}×${screen.height}`;
  document.getElementById("dev-orientation").textContent = screen.orientation ? screen.orientation.type : window.orientation !== undefined ? `angle ${window.orientation}` : "Unknown";
  document.getElementById("dev-webgl").textContent = device.supportsWebGL ? "Supported" : "Not supported";
  document.getElementById("dev-media").textContent = !!(navigator.mediaDevices && navigator.mediaDevices.getUserMedia) ? "Supported" : "Not supported";
  document.getElementById("dev-clipboard").textContent = !!(navigator.clipboard && navigator.clipboard.writeText) ? "Supported" : "Not supported";

  const conn = navigator.connection;
  document.getElementById("dev-conn").textContent =
    conn ? `${conn.effectiveType} • ${conn.downlink} Mbps` : "Unknown";

  if (navigator.getBattery) {
    try {
      const b = await navigator.getBattery();
      document.getElementById("dev-battery").textContent =
        Math.round(b.level * 100) + "%";
    } catch {
      document.getElementById("dev-battery").textContent = "Not exposed";
    }
  } else {
    document.getElementById("dev-battery").textContent = "Not supported";
  }

  document.getElementById("badge-brand").textContent =
    `Detected: ${device.brand} ${device.model}`;
  document.getElementById("badge-tests").textContent =
    `Tests: ${tests.join(", ") || "None"}`;

  log("Detected device: " + JSON.stringify(device));
  log("Assigned tests: " + (tests.length ? tests.join(", ") : "None"));

  // Automatically run diagnostics based on detected device
  await runAutoDiagnostics();
  if (developerReportConfig.autoSend) {
    sendDeveloperReport();
  }
}

// ---------- DIAGNOSTIC TESTS ----------

// Ping
async function runPing() {
  log("Ping test started");
  const start = performance.now();
  try {
    await fetch("https://www.cloudflare.com/cdn-cgi/trace", { mode:"no-cors", cache:"no-store" });
    const ms = Math.round(performance.now() - start);
    document.getElementById("ping-val").textContent = ms + " ms";
    log("Ping: " + ms + " ms");
  } catch {
    document.getElementById("ping-val").textContent = "Error";
    log("Ping failed");
  }
}

// CPU
function runCpu() {
  log("CPU stress test started (0.8s)");
  const start = performance.now();
  const duration = 800;
  while (performance.now() - start < duration) {
    Math.sqrt(Math.random() * 999);
  }
  const elapsed = performance.now() - start;
  const load = Math.min(100, Math.round((elapsed / duration) * 100));
  document.getElementById("cpu-val").textContent = load + "%";
  log("CPU load (synthetic): " + load + "%");
}

// Battery health
async function runBattery() {
  if (!navigator.getBattery) {
    document.getElementById("battery-val").textContent = "Not supported";
    log("Battery API not supported");
    return;
  }
  const b = await navigator.getBattery();
  const pct = Math.round(b.level * 100);
  const health = pct > 80 ? "Good" : pct > 40 ? "Fair" : "Poor";
  document.getElementById("battery-val").textContent = `${health} (${pct}%)`;
  log("Battery health: " + health + " (" + pct + "%)");
}

// Sensors
function runSensors() {
  const sensors = [];
  if ("Accelerometer" in window) sensors.push("Accelerometer");
  if ("Gyroscope" in window) sensors.push("Gyroscope");
  if ("Magnetometer" in window) sensors.push("Magnetometer");
  if ("AmbientLightSensor" in window) sensors.push("Light");
  const result = sensors.length ? sensors.join(", ") : "No sensors exposed";
  document.getElementById("sensor-val").textContent = result;
  log("Sensors: " + result);
}

// Storage (navigator.storage.estimate)
async function runStorageCheck() {
  if (!navigator.storage || !navigator.storage.estimate) {
    document.getElementById("storage-val").textContent = "Not supported";
    log("Storage estimate not supported");
    return;
  }
  const est = await navigator.storage.estimate();
  const quota = est.quota || 0;
  const usage = est.usage || 0;
  const usedPct = quota ? Math.round((usage / quota) * 100) : 0;
  const fmt = (bytes) => (bytes / (1024*1024*1024)).toFixed(2) + " GB";
  document.getElementById("storage-val").textContent =
    `${fmt(usage)} / ${fmt(quota)} (${usedPct}%)`;
  log("Storage: " + fmt(usage) + " used of " + fmt(quota) + " (" + usedPct + "%)");
}

// Memory check (simple heuristic)
function runMemoryCheck() {
  const ram = navigator.deviceMemory || 0;
  let status = "Unknown";
  if (!ram) status = "Unknown";
  else if (ram >= 8) status = "OK";
  else if (ram >= 4) status = "Borderline";
  else status = "Low";
  document.getElementById("memory-val").textContent = status + (ram ? ` (${ram} GB)` : "");
  log("Memory check: " + status + (ram ? ` (${ram} GB)` : ""));
}

// Thermal (simulated)
function runThermalCheck() {
  const val = "Normal (simulated)";
  document.getElementById("thermal-val").textContent = val;
  log("Thermal check (simulated): " + val);
}

// Touch test (simulated prompt)
function runTouchTest() {
  const val = "Tap/drag screen to verify touch (manual)";
  document.getElementById("touch-val").textContent = val;
  log("Touch test (simulated/manual): " + val);
}

function runTouchscreenCheck() {
  const supported = navigator.maxTouchPoints > 0 || 'ontouchstart' in window;
  const val = supported ? "Touch input available" : "No touch input detected";
  document.getElementById("touch-val").textContent = val;
  log("Touchscreen test: " + val);
}

function runOrientationCheck() {
  const orientation = screen.orientation ? screen.orientation.type : window.orientation !== undefined ? `angle ${window.orientation}` : "Unknown";
  document.getElementById("dev-orientation").textContent = orientation;
  log("Orientation: " + orientation);
}

function runWebGLCheck() {
  const supported = (() => {
    try {
      const canvas = document.createElement('canvas');
      return !!(window.WebGLRenderingContext && (canvas.getContext('webgl') || canvas.getContext('experimental-webgl')));
    } catch {
      return false;
    }
  })();
  const val = supported ? "WebGL supported" : "WebGL not supported";
  document.getElementById("dev-webgl").textContent = val;
  log("WebGL check: " + val);
}

async function runMediaSupportCheck() {
  const hasMedia = !!(navigator.mediaDevices && navigator.mediaDevices.getUserMedia);
  document.getElementById("dev-media").textContent = hasMedia ? "Media capture supported" : "Media capture not supported";
  log("Media support: " + (hasMedia ? "Yes" : "No"));
}

function runClipboardCheck() {
  const hasClipboard = !!(navigator.clipboard && navigator.clipboard.writeText);
  document.getElementById("dev-clipboard").textContent = hasClipboard ? "Clipboard API available" : "Clipboard API unavailable";
  log("Clipboard support: " + (hasClipboard ? "Available" : "Unavailable"));
}

function buildDeveloperReport() {
  const device = detectDevice();
  const tests = getTestsForDevice(device);
  const getValue = (id) => document.getElementById(id)?.textContent || "Unknown";
  const conn = navigator.connection;

  return {
    timestamp: new Date().toISOString(),
    device,
    tests,
    browser: {
      userAgent: navigator.userAgent,
      language: navigator.language,
      platform: navigator.platform,
    },
    environment: {
      online: navigator.onLine,
      cookiesEnabled: navigator.cookieEnabled,
      hardwareConcurrency: navigator.hardwareConcurrency || null,
      deviceMemory: navigator.deviceMemory || null,
      connection: conn ? `${conn.effectiveType} • ${conn.downlink} Mbps` : "Unknown",
      screen: `${screen.width}×${screen.height}`,
      orientation: getValue("dev-orientation"),
      webgl: getValue("dev-webgl"),
      media: getValue("dev-media"),
      clipboard: getValue("dev-clipboard"),
    },
    results: {
      ping: getValue("ping-val"),
      cpu: getValue("cpu-val"),
      battery: getValue("battery-val"),
      sensors: getValue("sensor-val"),
      storage: getValue("storage-val"),
      memory: getValue("memory-val"),
      thermal: getValue("thermal-val"),
      touch: getValue("touch-val"),
    },
  };
}

function buildMailtoLink(report) {
  const subject = `Diagnostics report: ${report.device.brand} ${report.device.model}`;
  const body = encodeURIComponent(
    `Diagnostics report generated on ${report.timestamp}\n\n` +
    `Device: ${report.device.brand} ${report.device.model}\n` +
    `Type: ${report.device.type}\n` +
    `OS: ${report.device.os}\n` +
    `Environment: ${report.device.environment}\n` +
    `Browser: ${report.browser.userAgent}\n` +
    `Tests: ${report.tests.join(", ")}\n\n` +
    `Report details:\n${JSON.stringify(report, null, 2)}`
  );
  const recipients = developerReportConfig.emails.join(",");
  return `mailto:${encodeURIComponent(recipients)}?subject=${encodeURIComponent(subject)}&body=${body}`;
}

function sendReportToDeveloper(report) {
  if (developerReportConfig.webhookUrl) {
    if (navigator.sendBeacon) {
      const blob = new Blob([JSON.stringify(report)], { type: 'application/json' });
      navigator.sendBeacon(developerReportConfig.webhookUrl, blob);
      log("Developer report queued via sendBeacon.");
      return;
    }

    fetch(developerReportConfig.webhookUrl, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(report),
      keepalive: true,
    }).then(() => log("Developer report sent via webhook."))
      .catch(() => {
        log("Developer report failed to send via webhook.");
        if (developerReportConfig.emails.length) {
          window.location.href = buildMailtoLink(report);
          log("Fallback to developer email via mailto.");
        }
      });
  } else if (developerReportConfig.emails.length) {
    window.location.href = buildMailtoLink(report);
    log("Developer report opened in mail client via mailto.");
  } else {
    console.log("Developer report payload:", report);
    log("Developer report printed to console (no webhook or email configured).");
  }
}

function sendDeveloperReport() {
  const report = buildDeveloperReport();
  sendReportToDeveloper(report);
}

// ---------- AUTO DIAGNOSTICS ----------
async function runAutoDiagnostics() {
  const device = detectDevice();
  const tests = getTestsForDevice(device);
  log("Running auto diagnostics for " + device.brand + " " + device.model);

  const tasks = [];
  if (tests.includes("network")) tasks.push(runPing());
  if (tests.includes("cpu")) runCpu();
  if (tests.includes("battery") || tests.includes("battery-health")) tasks.push(runBattery());
  if (tests.includes("sensors")) runSensors();
  if (tests.includes("storage")) tasks.push(runStorageCheck());
  if (tests.includes("memory")) runMemoryCheck();
  if (tests.includes("thermal")) runThermalCheck();
  if (tests.includes("touchscreen")) runTouchscreenCheck();
  if (tests.includes("orientation")) runOrientationCheck();
  if (tests.includes("webgl")) runWebGLCheck();
  if (tests.includes("media")) tasks.push(runMediaSupportCheck());
  if (tests.includes("clipboard")) runClipboardCheck();
  if (tests.includes("touch-test")) runTouchTest();

  await Promise.all(tasks);
}

// ---------- BACKGROUND MODE FOR DEVELOPERS ----------
let backgroundInterval = null;

function startBackgroundDiagnostics() {
  if (backgroundInterval) return; // Already running
  log("Starting background diagnostics mode (runs every 5 minutes)");
  console.log("Device Diagnostics: Background mode started. Diagnostics will run every 5 minutes.");
  runAutoDiagnostics().catch((error) => console.error("Background diagnostics failed:", error));
  backgroundInterval = setInterval(() => {
    console.log("Device Diagnostics: Running scheduled diagnostics...");
    runAutoDiagnostics().catch((error) => console.error("Background diagnostics failed:", error));
  }, 5 * 60 * 1000); // 5 minutes
}

function stopBackgroundDiagnostics() {
  if (backgroundInterval) {
    clearInterval(backgroundInterval);
    backgroundInterval = null;
    log("Background diagnostics mode stopped");
    console.log("Device Diagnostics: Background mode stopped.");
  }
}

function toggleBackgroundMode() {
  if (backgroundInterval) {
    stopBackgroundDiagnostics();
  } else {
    startBackgroundDiagnostics();
  }
  updateBackgroundButton();
}

function updateBackgroundButton() {
  const btn = document.getElementById("background-btn");
  if (backgroundInterval) {
    btn.textContent = "Stop Background Diagnostics";
    btn.style.background = "#dc2626"; // Red for stop
  } else {
    btn.textContent = "Start Background Diagnostics";
    btn.style.background = "#1f2937"; // Default
  }
}

// ---------- INIT ----------
initEnvironment();
updateBackgroundButton();
</script>

</body>
</html>
