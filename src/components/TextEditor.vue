<template>
  <div
      id="textEditorContainer"
      :class="{disabled}"
  >
    <div class="text-editor-header">
      <div class="text-editor-btn-list">
        <button
            v-for="(item) of buttonList"
            :key="item"
            class="text-editor-btn"
            :class="{'active': activeList[item.style]}"
            @click="onClickTopButton(item.style)"
        >
          <component :is="iconList[item.style]" />
        </button>
        <select
            ref="fontSizeRef"
            class="form-select"
            @change="onChangeFontSize"
        >
          <option value="1">
            10px
          </option>
          <option value="2">
            13px
          </option>
          <option value="3">
            16px
          </option>
          <option value="4">
            18px
          </option>
          <option value="5">
            24px
          </option>
          <option value="6">
            32px
          </option>
          <option value="7">
            48px
          </option>
        </select>
        <select
            ref="fontNameRef"
            class="form-select"
            @change="onChangeFontName"
        >
          <option value="Noto Sans CJK, sans-serif">
            Noto Sans CJK
          </option>
          <option value="Roboto">
            Roboto
          </option>
        </select>
        <template v-if="pageType === 'LAW'">
          <button
              class="text-editor-btn"
              :disabled="isTable"
              @click="onClickSetTable('insert')"
          >
            <InsertTableSVG />
          </button>
          <button
              class="text-editor-btn"
              :disabled="!isTable"
              @click="onClickSetTable('before')"
          >
            <InsertBeforeRowSVG />
          </button>
          <button
              class="text-editor-btn"
              :disabled="!isTable"
              @click="onClickSetTable('after')"
          >
            <InsertAfterRowSVG />
          </button>
          <button
              class="text-editor-btn"
              :disabled="!isTable"
              @click="onClickSetTable('delete')"
          >
            <DeleteRowSVG />
          </button>
        </template>
      </div>
      <div class="text-editor-btn-list">
        <button
            class="text-editor-btn"
            @click="onClickTopButton('undo')"
        >
          <UndoSVG />
        </button>
        <button
            class="text-editor-btn"
            @click="onClickTopButton('redo')"
        >
          <RedoSVG />
        </button>
      </div>
    </div>
    <div
        id="textEditor"
        ref="editorRef"
        :contenteditable="!disabled"
        @keydown="onKeydown"
        @mousedown="onMousedown"
        @paste="onPaste"
        @drop.prevent
    />
  </div>
</template>

<script setup>
import { defineAsyncComponent, markRaw, ref, watch } from 'vue';

import DeleteRowSVG from './svg/DeleteRowSVG.vue';
import InsertAfterRowSVG from './svg/InsertAfterRowSVG.vue';
import InsertBeforeRowSVG from './svg/InsertBeforeRowSVG.vue';
import InsertTableSVG from './svg/InsertTableSVG.vue';
import RedoSVG from './svg/RedoSVG.vue';
import UndoSVG from './svg/UndoSVG.vue';

const iconList = {
  bold: markRaw(defineAsyncComponent(() => import('./svg/BoldSVG.vue'))),
  italic: markRaw(defineAsyncComponent(() => import('./svg/ItalicSVG.vue'))),
  underline: markRaw(defineAsyncComponent(() => import('./svg/UnderlineSVG.vue'))),
  justifyLeft: markRaw(defineAsyncComponent(() => import('./svg/AlignLeftSVG.vue'))),
  justifyCenter: markRaw(defineAsyncComponent(() => import('./svg/AlignCenterSVG.vue'))),
  justifyRight: markRaw(defineAsyncComponent(() => import('./svg/AlignRightSVG.vue'))),
  insertOrderedList: markRaw(defineAsyncComponent(() => import('./svg/ListOrderedSVG.vue'))),
  insertUnorderedList: markRaw(defineAsyncComponent(() => import('./svg/ListUnorderedSVG.vue'))),
};
const buttonList = [
  { style: 'bold' },
  { style: 'italic' },
  { style: 'underline' },
  { style: 'justifyLeft' },
  { style: 'justifyCenter' },
  { style: 'justifyRight' },
  { style: 'insertOrderedList' },
  { style: 'insertUnorderedList' },
].map((item) => {
  return { ...item, active: false };
});
const activeList = ref({});

const props = defineProps({
  disabled: {
    type: Boolean,
    default: false,
  },
  guideType: {
    type: String,
    default: '',
  },
  pageType: {
    type: String,
    default: '',
  },
});

const fontSizeRef = ref(null);
const fontNameRef = ref(null);
const editorRef = ref(null);

const isTable = ref(false);

defineExpose({ editorRef });

const fontSizeList = [10, 13, 16, 18, 24, 32, 48];

const getComputedStyleProperty = (el, propName) => {
  if (window.getComputedStyle) {
    return window.getComputedStyle(el, null)[propName];
  } else if (el.currentStyle) {
    return el.currentStyle[propName];
  }
};
const checkFont = () => {
  let containerEl; let sel;
  if (window.getSelection) {
    sel = window.getSelection();
    if (sel.rangeCount) {
      containerEl = sel.getRangeAt(0).commonAncestorContainer;
      if (containerEl.nodeType === 3) {
        containerEl = containerEl.parentNode;
      }
    }
  } else if ((sel = document.selection) && sel.type !== 'Control') {
    containerEl = sel.createRange().parentElement();
  }
  // if (containerEl.id) return;

  if (containerEl) {
    const fontSize = getComputedStyleProperty(containerEl, 'fontSize');
    const fontName = getComputedStyleProperty(containerEl, 'fontFamily');
    const size = parseInt(fontSize.replace('px', ''));

    fontSizeRef.value.value = fontSizeList.indexOf(size) + 1;
    fontNameRef.value.value = fontName.replaceAll('"', '');
  }
};
const checkStyle = () => {
  setTimeout(() => {
    buttonList.forEach((item) => {
      if (!item.style.includes('justify')) activeList.value[item.style] = isStyle(item.style);
    });
    checkFont();
  }, 50);
};
const checkTable = () => {
  setTimeout(() => {
    isTable.value = currentNode()?.offsetParent.nodeName === 'TABLE';
  }, 50);
};
const currentNode = () => {
  let containerEl; let sel;
  if (window.getSelection) {
    sel = window.getSelection();
    if (sel.rangeCount) {
      containerEl = sel.getRangeAt(0).commonAncestorContainer;
      if (containerEl.nodeType === 3) {
        containerEl = containerEl.parentNode;
      }
    }
  }

  return containerEl;
};
const focusEditor = () => {
  editorRef.value.focus({ preventScroll: true });
};
const isStyle = (style) => {
  return document.queryCommandState(style);
};
const onChangeFontName = (event) => {
  document.execCommand('fontName', false, event.target.value);
  focusEditor();
};
const onChangeFontSize = (event) => {
  document.execCommand('fontSize', false, event.target.value);
  focusEditor();
};
const onClickSetTable = (type) => {
  focusEditor();
  if (type === 'insert') {
    document.execCommand('insertHTML', false, '<table class="text-editor-table"><tbody><tr><th></th><td></td></tr></tbody></table>');
  } else if (type === 'before' || type === 'after') {
    const table = currentNode().offsetParent;
    const trList = table.children[0].children;
    const trIndex = currentNode().parentNode.rowIndex;
    let trStr = '';
    for (let i = 0; i < table.children[0].children.length; i++) {
      if (type === 'before' && i === trIndex) trStr += '<tr><th></th><td></td></tr>';
      trStr += trList[i].outerHTML;
      if (type === 'after' && i === trIndex) trStr += '<tr><th></th><td></td></tr>';
    }
    const dummy = `<table class="text-editor-table"><tbody>${trStr}</tbody></table>`;
    window.getSelection().setBaseAndExtent(currentNode().offsetParent, 0, currentNode().offsetParent, 1);
    document.execCommand('insertHTML', false, dummy);
  } else if (type === 'delete') {
    const table = currentNode().offsetParent;
    const trList = table.children[0].children;
    const trIndex = currentNode().parentNode.rowIndex;
    let trStr = '';

    for (let i = 0; i < table.children[0].children.length; i++) {
      if (i !== trIndex) trStr += trList[i].outerHTML;
    }

    const dummy = `<table class="text-editor-table"><tbody>${trStr}</tbody></table>`;
    window.getSelection().setBaseAndExtent(currentNode().offsetParent, 0, currentNode().offsetParent, 1);
    document.execCommand('insertHTML', false, dummy);
  }
};
const onClickTopButton = (style, value = null) => {
  document.execCommand(style, false, value);

  focusEditor();
  checkStyle();
};
const onKeydown = (event) => {
  checkStyle();
  checkTable(event);
};
const onMousedown = (event) => {
  checkStyle();
  checkTable(event);
};
const onPaste = (event) => {
  const paste = (event.clipboardData || window.clipboardData).getData('text');

  if (!paste) event.preventDefault(); // 이미지 방지
};

watch(() => props.guideType, (value) => {
  editorRef.value.innerHTML = policyStore.body[value] || '';
});
watch(() => props.pageType, (value) => {
  if (value.toString() !== 'GUIDE') editorRef.value.innerHTML = policyStore.body[value] || '';
});
</script>
