
  VSCode TypeScript + ESM Setup Guide (with `ts-node` and Debugging)

This guide sets up a clean TypeScript development environment in VSCode using:

- **Node.js** with native **ESM**
- **ts-node** for on-the-fly TypeScript execution
- **VSCode `launch.json`** for debugging
- **Code Runner plugin** for quick `.ts` execution
- Clean `tsconfig.json` using:  
  ```bash
  tsc --init --sourceMap --rootDir src --outDir dist .
</code></pre>
<hr>
<h2>✅ 1. Initialize Your Project</h2>
<pre><code class="language-bash">npm init -y
npm install -D typescript ts-node
</code></pre>
<hr>
<h2>✅ 2. Setup <code inline="">tsconfig.json</code></h2>
<p>Run:</p>
<pre><code class="language-bash">tsc --init --sourceMap --rootDir src --outDir dist .
</code></pre>
<p>Then <strong>edit</strong> your <code inline="">tsconfig.json</code> like this:</p>
<pre><code class="language-json">{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "moduleResolution": "node",
    "outDir": "dist",
    "rootDir": "src",
    "sourceMap": true,
    "esModuleInterop": true,
    "strict": true
  },
  "ts-node": {
    "esm": true
  },
  "include": ["src"]
}
</code></pre>
<hr>
<h2>✅ 3. Add <code inline="">"type": "module"</code> to <code inline="">package.json</code></h2>
<p>This is required for native ESM support:</p>
<pre><code class="language-json">{
  "type": "module"
}
</code></pre>
<hr>
<h2>✅ 4. Setup VSCode <code inline="">launch.json</code> (F5 to Debug)</h2>
<ol>
<li>
<p>Press <code inline="">Ctrl+Shift+D</code> in VSCode → Create a <code inline="">launch.json</code> file</p>
</li>
<li>
<p>Replace with:</p>
</li>
</ol>
<pre><code class="language-json">{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Debug TypeScript (ESM)",
      "runtimeExecutable": "node",
      "runtimeArgs": [
        "--loader",
        "ts-node/esm"
      ],
      "program": "${workspaceFolder}/src/index.ts",
      "cwd": "${workspaceFolder}",
      "console": "integratedTerminal",
      "skipFiles": ["&lt;node_internals&gt;/**"]
    }
  ]
}
</code></pre>
<blockquote>
<p>🔹 Adjust <code inline="">src/index.ts</code> if your entry file differs.</p>
</blockquote>
<hr>
<h2>✅ 5. Setup <code inline="">settings.json</code> for Code Runner Plugin</h2>
<p>To run <code inline="">.ts</code> files using <strong>Ctrl+Alt+N</strong> or the <strong>Run Code</strong> button:</p>
<ol>
<li>
<p>Open Command Palette → <code inline="">Preferences: Open Settings (JSON)</code></p>
</li>
<li>
<p>Add this to your <code inline="">settings.json</code>:</p>
</li>
</ol>
<pre><code class="language-json">{
  "code-runner.executorMap": {
    "typescript": "node --no-warnings --loader ts-node/esm"
  },
  "code-runner.runInTerminal": true,
  "code-runner.clearPreviousOutput": true
}
</code></pre>
<hr>
<h2>✅ 6. Test It</h2>
<p>Create a test file: <code inline="">src/index.ts</code></p>
<pre><code class="language-ts">console.log("✅ TypeScript + ESM is working!");
</code></pre>
<ul>
<li>
<p>Press <code inline="">F5</code> to <strong>debug</strong></p>
</li>
<li>
<p>Press <code inline="">Ctrl+Alt+N</code> to <strong>run via Code Runner</strong></p>
</li>
</ul>
<hr>
<h2>📦 Summary</h2>

Feature | Config File | Purpose
-- | -- | --
TypeScript Project | tsconfig.json | Compiler + ts-node settings
ESM Support | package.json | "type": "module" for ESM
Debug with F5 | launch.json | Use ts-node/esm loader
Run with Code Runner | settings.json | Run .ts via Code Runner plugin


<hr>
<h2>✅ Done!</h2>
<p>You now have a modern VSCode setup for TypeScript + ESM with debugging and fast script execution.</p>
 
 

 
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIwMzEyMTUyNTBdfQ==
-->