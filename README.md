XIG API Documentation
-
This documentation is designed to be edited with the Visual Studio Code editor.
You will also need to install `npm`.
Once you have installed `npm`, you will need to use that to install `markdown-it` and `markdown-pdf`.

You can do that by using the following commands:
```
npm install -g markdown-it
npm install -g markdown-pdf
```
You may need to invoke those commands with `sudo` for them to work.

Once you have those installed, you may edit and preview the document freely.
When you want to render it, press `Shift+Command+P` (or, on Windows, `Shift+Control+P`) to bring up the Command Palette, then enter `Tasks: Run Task` in the menu, and select the option to bring up the Task Runner. Select either `markdown-it` to render an HTML (web) version, or `markdown-pdf` to generate a more printable PDF option. You may also use `Shift+Command+B` to render a PDF, as `markdown-pdf` has been marked as a build task.

There are several flavors of Markdown, so no reference will be comprehensive. [This](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) is a good start.