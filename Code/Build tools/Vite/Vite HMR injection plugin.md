#vite #web #code #plugin 

```typescript
function hmrAutoAcceptPlugin(): PluginOption {
	const injectionLine = "if(import.meta.hot){import.meta.hot.accept()}";
	const fileRegex = /\.(m?jsx?|tsx?)$/;
	
	return {
		name: "hmr-auto-accept",
		apply: "serve",
		enforce: "post",
		transform(code, id) {
			if (!fileRegex.test(id)) return null;
			if (id.endsWith(".d.ts")) return null;
			if (id.includes("node_modules")) return null;
			if (id.startsWith("\0") || id.includes("virtual:") || id.includes("/@vite/")) return null;
			if (code.includes("import.meta.hot.accept()")) return null;
			
			if (!code.endsWith("\n")) {
				code += "\n";
			}
			code += injectionLine;
			return { code: code, map: null };
		},
	};
}
```