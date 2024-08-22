<template>
  <div class="editor-container" ref="editorContainer"></div>
</template>

<script>
import * as monaco from 'monaco-editor';

export default {
  name: 'MonacoEditor',
  props: {
    value: {
      type: String,
      default: ''
    },
    language: {
      type: String,
      default: 'modelica'
    },
    theme: {
      type: String,
      default: 'vs-dark'
    }
  },
  mounted() {
    this.editor = monaco.editor.create(this.$refs.editorContainer, {
      value: this.value,
      language: this.language,
      theme: this.theme,
    });

    // 监听编辑器内容变化
    this.editor.onDidChangeModelContent(() => {
      this.$emit('input', this.editor.getValue());
    });

    // 添加 Modelica 支持
    this.addModelicaSupport();
  },
  methods: {
    addModelicaSupport() {
      monaco.languages.register({ id: 'modelica' });

      monaco.languages.setMonarchTokensProvider('modelica', {
        keywords: [
          // ... 这里添加 Modelica 的关键字
        ],
        typeKeywords: [
          'Boolean', 'Integer', 'Real', 'String'
        ],
        operators: [
          '=', '+', '-', '*', '/', '^', '<', '<=', '>', '>=', '<>', '==', 'and', 'or', 'not'
        ],
        symbols: /[=><!~?:&|+\-*\/\^%]+/,

        tokenizer: {
          root: [
            [/[a-zA-Z_][\w]*/, {
              cases: {
                '@keywords': 'keyword',
                '@typeKeywords': 'keyword.type',
                '@default': 'identifier'
              }
            }],
            [/@symbols/, { cases: { '@operators': 'operator', '@default': '' } }],
            { include: '@whitespace' },
            [/[{}()\[\]]/, '@brackets'],
            [/\d*\.\d+([eE][\-+]?\d+)?/, 'number.float'],
            [/\d+/, 'number'],
            [/".*"/, 'string'],
            [/\/\/.*$/, 'comment'],
            [/\/\*/, 'comment', '@comment'],
          ],

          comment: [
            [/[^\/*]+/, 'comment'],
            [/\*\//, 'comment', '@pop'],
            [/[\/*]/, 'comment']
          ],

          whitespace: [
            [/[ \t\r\n]+/, 'white'],
            [/\/\*/, 'comment', '@comment'],
            [/\/\/.*$/, 'comment'],
          ],
        },
      });

      // 配置 Modelica 的自动完成功能
      monaco.languages.registerCompletionItemProvider('modelica', {
        provideCompletionItems: function(model, position) {
          const suggestions = [
            {
              label: 'model',
              kind: monaco.languages.CompletionItemKind.Keyword,
              insertText: [
                'model ${1:ModelName}',
                '  ${2:// variables}',
                '',
                '  equation',
                '    ${3:// equations}',
                '',
                'end ${1:ModelName};'
              ].join('\n'),
              insertTextRules: monaco.languages.CompletionItemInsertTextRule.InsertAsSnippet,
              documentation: 'Define a new model'
            },
            // 你可以在这里添加更多代码片段
          ];
          return { suggestions: suggestions };
        }
      });
    }
  },
  beforeDestroy() {
    if (this.editor) {
      this.editor.dispose();
    }
  }
};
</script>

<style>
.editor-container {
  width: 100%;
  height: 100%;
}
</style>