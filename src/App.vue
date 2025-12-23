﻿<template>
  <div class="flex flex-col h-screen bg-[#f1f5f9] font-sans text-slate-700 relative overflow-hidden selection:bg-teal-500/30 selection:text-teal-900">
    
    <!-- === 1. 沉浸式背景装饰层 === -->
    <div class="absolute inset-0 z-0 pointer-events-none overflow-hidden">
      <!-- 噪点纹理 -->
      <div class="absolute inset-0 bg-[url('https://grainy-gradients.vercel.app/noise.svg')] opacity-20 brightness-100 contrast-150 mix-blend-overlay"></div>
      <!-- 点阵网格 -->
      <div class="absolute inset-0" 
           style="background-image: radial-gradient(#cbd5e1 1px, transparent 1px); background-size: 32px 32px; opacity: 0.4;">
      </div>
      
      <!-- 动态流光光斑 -->
      <div class="absolute top-[-20%] right-[-10%] w-[600px] h-[600px] bg-gradient-to-br from-teal-200/40 to-cyan-200/40 rounded-full mix-blend-multiply filter blur-[80px] animate-float-slow"></div>
      <div class="absolute bottom-[-10%] left-[-10%] w-[500px] h-[500px] bg-gradient-to-tr from-blue-200/40 to-indigo-200/40 rounded-full mix-blend-multiply filter blur-[80px] animate-float-delayed"></div>
    </div>

    <!-- === 2. 顶部导航栏 === -->
    <header class="relative z-30 flex items-center justify-between px-6 py-4 bg-white/70 backdrop-blur-xl border-b border-white/50 shadow-sm sticky top-0 transition-all duration-300">
      <div class="flex items-center gap-4">
        <div class="relative group cursor-pointer">
          <div class="absolute -inset-1 bg-gradient-to-r from-teal-400 to-blue-500 rounded-xl blur opacity-25 group-hover:opacity-50 transition duration-500"></div>
          <div class="relative w-11 h-11 rounded-xl shadow-inner overflow-hidden bg-white p-0.5 ring-1 ring-white/50">
            <img src="/icon/泉策通图标.png" alt="泉策通" class="w-full h-full object-cover rounded-[10px]" />
          </div>
          <span class="absolute -bottom-0.5 -right-0.5 flex h-3 w-3">
            <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-teal-400 opacity-75"></span>
            <span class="relative inline-flex rounded-full h-3 w-3 bg-teal-500 border-2 border-white"></span>
          </span>
        </div>
        
        <div class="flex flex-col">
          <h1 class="font-bold text-lg text-slate-800 tracking-tight flex items-center gap-2">
            泉策通
            <span class="text-xs font-normal text-slate-400">/ Smart Agent</span>
            <span class="ml-1 px-2 py-0.5 text-[10px] font-bold tracking-wider bg-gradient-to-r from-teal-500 to-emerald-500 text-white rounded-full shadow-lg shadow-teal-500/20">PRO</span>
          </h1>
          <p class="text-[11px] font-medium text-slate-400 uppercase tracking-widest flex items-center gap-1">
             <span class="w-1 h-1 rounded-full bg-slate-300"></span>
             山东省政策数据大脑
          </p>
        </div>
      </div>

      <button class="hidden sm:flex group items-center gap-2 px-4 py-2 bg-white/50 hover:bg-white/80 border border-white/60 shadow-sm hover:shadow text-xs font-medium text-slate-500 hover:text-teal-600 rounded-full transition-all duration-300 backdrop-blur-md" @click="clearHistory">
         <span class="w-4 h-4 flex items-center justify-center rounded-full bg-slate-100 group-hover:bg-teal-50 transition-colors">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-3 w-3" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" /></svg>
         </span>
         <span>重置会话</span>
      </button>
    </header>

    <!-- === 3. 消息列表区域 === -->
    <main class="relative z-10 flex-1 overflow-y-auto p-4 sm:p-6 scroll-smooth custom-scrollbar" ref="messagesContainer">
      <div class="max-w-4xl mx-auto space-y-8 pb-8">
        
        <!-- === 欢迎页 === -->
        <div v-if="messages.length === 0" class="mt-8 sm:mt-16 animate-fade-in-up">
          <div class="text-center mb-12 relative">
             <div class="inline-flex items-center justify-center p-4 bg-white/40 backdrop-blur-sm border border-white/60 rounded-[2rem] shadow-xl shadow-teal-100/30 mb-6 relative">
                <div class="absolute inset-0 rounded-[2rem] bg-gradient-to-tr from-teal-500/10 to-transparent blur-xl"></div>
                <img src="/icon/泉策通图标.png" class="w-16 h-16 object-cover rounded-2xl relative z-10" />
             </div>
             <h2 class="text-4xl font-black text-slate-800 tracking-tight mb-4">
               政策咨询，<span class="text-transparent bg-clip-text bg-gradient-to-r from-teal-500 to-blue-600">触手可及</span>
             </h2>
             <p class="text-slate-500 max-w-lg mx-auto text-base leading-relaxed">
               基于 RAG 检索增强生成技术，为您提供精准的山东省惠企利民政策解读、补贴计算与申报指引。
             </p>
          </div>

          <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 max-w-3xl mx-auto">
            <div v-for="(item, idx) in quickActions" :key="idx"
              @click="setInput(item.text)"
              class="group relative overflow-hidden bg-white/60 backdrop-blur-md border border-white/60 p-5 rounded-2xl shadow-sm hover:shadow-xl hover:shadow-teal-900/5 hover:border-teal-200/60 hover:-translate-y-1 transition-all duration-300 cursor-pointer"
            >
              <div class="absolute top-0 right-0 w-24 h-24 bg-gradient-to-br from-transparent to-slate-50 opacity-50 rounded-bl-[4rem] transition-transform group-hover:scale-110 group-hover:to-teal-50/50"></div>
              
              <div class="relative flex items-center gap-4">
                <div :class="`flex-shrink-0 w-12 h-12 rounded-xl flex items-center justify-center ${item.color} text-white shadow-lg shadow-gray-200 group-hover:scale-110 transition-transform duration-300`">
                   <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                     <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" :d="item.path" />
                   </svg>
                </div>
                <div>
                  <h3 class="font-bold text-slate-800 text-sm group-hover:text-teal-700 transition-colors">{{ item.title }}</h3>
                  <p class="text-xs text-slate-500 mt-1 pr-4">{{ item.desc }}</p>
                </div>
                <div class="absolute right-4 top-1/2 -translate-y-1/2 opacity-0 -translate-x-2 group-hover:opacity-100 group-hover:translate-x-0 transition-all duration-300">
                   <svg class="w-5 h-5 text-teal-400" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" /></svg>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- === 对话流 === -->
        <div v-for="(msg, index) in messages" :key="index" 
             :class="['flex gap-5 animate-slide-in', msg.role === 'user' ? 'flex-row-reverse' : 'flex-row']">
          
          <!-- 头像 -->
          <div class="flex-shrink-0 h-11 w-11 rounded-full flex items-center justify-center shadow-lg shadow-slate-200/50 overflow-hidden border-2 border-white relative z-10"
               :class="msg.role === 'user' ? 'bg-slate-800' : 'bg-white'">
            <span v-if="msg.role === 'user'" class="text-white">
               <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M10 9a3 3 0 100-6 3 3 0 000 6zm-7 9a7 7 0 1114 0H3z" clip-rule="evenodd" /></svg>
            </span>
            <img v-else src="/icon/泉策通图标.png" alt="泉策通" class="w-full h-full object-cover" />
          </div>

          <!-- 气泡 (双重玻璃质感) -->
          <div :class="['relative max-w-[85%] sm:max-w-[80%] px-6 py-5 shadow-lg text-[15px] leading-7 transition-all duration-300 group overflow-hidden', 
               msg.role === 'user' 
               ? 'rounded-[24px] rounded-tr-sm text-white' 
               : 'rounded-[24px] rounded-tl-sm text-slate-700']">
            
            <!-- A. 用户气泡背景：深海琉璃质感 (透明度提高，增加光泽) -->
            <div v-if="msg.role === 'user'" class="absolute inset-0 z-0">
               <!-- 1. 强模糊背景 -->
               <div class="absolute inset-0 bg-slate-900/60 backdrop-blur-xl"></div>
               <!-- 2. 颜色倾向：深青色玻璃 -->
               <div class="absolute inset-0 bg-gradient-to-br from-teal-600/60 to-blue-700/60"></div>
               <!-- 3. 玻璃边框：半透明白色描边 -->
               <div class="absolute inset-0 border border-white/20 rounded-[24px] rounded-tr-sm pointer-events-none"></div>
               <!-- 4. 顶部高光流光 -->
               <div class="absolute inset-0 bg-gradient-to-b from-white/10 to-transparent pointer-events-none"></div>
            </div>

            <!-- B. AI 气泡背景：白玉玻璃质感 -->
            <div v-else class="absolute inset-0 z-0">
               <!-- 1. 强模糊背景 -->
               <div class="absolute inset-0 bg-white/70 backdrop-blur-xl support-[backdrop-filter]:bg-white/60"></div>
               <!-- 2. 颜色倾向：纯净白 -->
               <div class="absolute inset-0 bg-gradient-to-b from-white/80 to-white/40"></div>
               <!-- 3. 玻璃边框 -->
               <div class="absolute inset-0 border border-white/60 rounded-[24px] rounded-tl-sm pointer-events-none"></div>
            </div>

            <!-- 内容层 -->
            <div class="relative z-10">
              <div v-if="msg.role === 'user'" class="font-medium tracking-wide drop-shadow-sm text-shadow-sm">{{ msg.content }}</div>
              
              <div v-else>
                 <!-- 错误信息处理 -->
                 <div v-if="msg.content.includes('System Error') || msg.content.includes('Failed to fetch')" class="flex items-center gap-2 text-red-500 bg-red-50/50 p-3 rounded-lg border border-red-100/50">
                    <svg class="w-5 h-5 flex-shrink-0" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" /></svg>
                    <span>{{ msg.content }}</span>
                 </div>
                 <div v-else class="markdown-body" v-html="renderMarkdown(msg.content)"></div>
                 
                 <span v-if="!msg.done && index === messages.length - 1" class="inline-block w-2 h-5 ml-1 align-middle bg-teal-500 animate-pulse rounded-sm shadow-[0_0_10px_rgba(20,184,166,0.5)]"></span>
              </div>
              
              <!-- 图表区域 -->
              <div v-if="msg.role === 'ai' && (msg.chartUrl && msg.showChart || msg.chartUrl2 && msg.showChart2)" class="mt-6 grid gap-4 grid-cols-1 sm:grid-cols-2">
                 <div v-if="msg.chartUrl && msg.showChart" class="col-span-1 rounded-2xl overflow-hidden border border-white/50 shadow-lg bg-white/50 backdrop-blur-md group/chart hover:shadow-xl transition-all duration-300">
                    <div class="px-4 py-2 border-b border-white/30 bg-white/40 flex items-center justify-between">
                       <div class="flex items-center gap-2">
                          <span class="relative flex h-2 w-2">
                            <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-teal-400 opacity-75"></span>
                            <span class="relative inline-flex rounded-full h-2 w-2 bg-teal-500"></span>
                          </span>
                          <p class="text-[10px] font-bold text-slate-500 uppercase tracking-wider">Data Analytics</p>
                       </div>
                    </div>
                    <div class="p-2 relative">
                      <img :src="msg.chartUrl" alt="数据图表" class="w-full h-auto rounded-xl mix-blend-multiply" />
                    </div>
                 </div>
                 
                 <div v-if="msg.chartUrl2 && msg.showChart2" class="col-span-1 rounded-2xl overflow-hidden border border-white/50 shadow-lg bg-white/50 backdrop-blur-md group/chart hover:shadow-xl transition-all duration-300">
                     <div class="px-4 py-2 border-b border-white/30 bg-white/40 flex items-center justify-between">
                       <div class="flex items-center gap-2">
                          <span class="w-2 h-2 rounded-full bg-orange-400"></span>
                          <p class="text-[10px] font-bold text-slate-500 uppercase tracking-wider">Geo Map</p>
                       </div>
                    </div>
                    <div class="p-2">
                      <img :src="msg.chartUrl2" alt="地图图表" class="w-full h-auto rounded-xl mix-blend-multiply" />
                    </div>
                 </div>
              </div>

              <!-- 引用来源 -->
              <div v-if="msg.role === 'ai' && msg.references?.length && msg.done" class="mt-5 pt-4 border-t border-slate-200/40">
                <p class="flex items-center gap-2 text-xs font-bold text-slate-400 mb-3 uppercase tracking-wider">
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 text-teal-600" viewBox="0 0 20 20" fill="currentColor"><path d="M9 4.804A7.968 7.968 0 005.5 4c-1.255 0-2.443.29-3.5.804v10A7.969 7.969 0 015.5 14c1.669 0 3.218.51 4.5 1.385A7.962 7.962 0 0114.5 14c1.255 0 2.443.29 3.5.804v-10A7.968 7.968 0 0014.5 4c-1.255 0-2.443.29-3.5.804V12a1 1 0 11-2 0V4.804z"/></svg>
                  Reference Sources
                </p>
                <div class="flex flex-wrap gap-2">
                   <div v-for="ref in msg.references" :key="ref.id || ref.text" 
                        class="relative group/ref flex items-center gap-2 bg-white/60 border border-white/60 pl-1.5 pr-3 py-1 rounded-lg hover:bg-white hover:border-teal-400 shadow-sm transition-all cursor-pointer backdrop-blur-sm">
                      <span class="flex items-center justify-center w-5 h-5 text-[10px] font-bold text-white bg-slate-400/80 rounded group-hover/ref:bg-teal-500 transition-colors">{{ ref.id }}</span>
                      <span class="text-xs text-slate-600 truncate max-w-[150px] group-hover/ref:text-slate-800">{{ ref.text }}</span>
                      <div class="absolute bottom-full left-1/2 -translate-x-1/2 mb-2 w-64 p-2 bg-slate-800 text-white text-[10px] rounded shadow-xl opacity-0 invisible group-hover/ref:opacity-100 group-hover/ref:visible transition-all z-20 pointer-events-none">
                         {{ ref.text }}
                         <div class="absolute top-full left-1/2 -translate-x-1/2 border-4 border-transparent border-t-slate-800"></div>
                      </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- 思考状态 -->
        <div v-if="isThinking && !isStreaming" class="flex gap-5 animate-pulse">
           <div class="flex-shrink-0 h-11 w-11 bg-white rounded-full flex items-center justify-center shadow-lg border-2 border-white">
             <img src="/icon/泉策通图标.png" alt="泉策通" class="w-full h-full object-cover opacity-50 grayscale" />
          </div>
          <div class="bg-white/80 backdrop-blur-md border border-white rounded-2xl px-6 py-4 shadow-sm flex items-center gap-3">
             <div class="flex gap-1">
               <span class="w-2 h-2 bg-teal-400 rounded-full animate-bounce"></span>
               <span class="w-2 h-2 bg-teal-400 rounded-full animate-bounce delay-100"></span>
               <span class="w-2 h-2 bg-teal-400 rounded-full animate-bounce delay-200"></span>
            </div>
            <span class="text-sm font-medium text-slate-500">正在调用知识库引擎...</span>
          </div>
        </div>
      </div>
    </main>

    <!-- === 4. 底部输入区 === -->
    <footer class="relative z-30 p-4 sm:p-6 pb-6">
      <div class="max-w-4xl mx-auto relative group">
        <div class="absolute -inset-1 bg-gradient-to-r from-teal-400 via-blue-400 to-indigo-400 rounded-2xl opacity-0 blur-lg group-focus-within:opacity-30 transition duration-1000"></div>
        
        <div class="relative bg-white/80 backdrop-blur-xl rounded-[1.2rem] shadow-[0_8px_30px_rgb(0,0,0,0.04)] border border-white/60 flex flex-col transition-all duration-300 group-focus-within:bg-white group-focus-within:shadow-[0_8px_40px_rgba(20,184,166,0.1)]">
          
          <textarea 
            v-model="userInput"
            @keydown.enter.prevent="handleEnter"
            placeholder="输入政策问题，例如：济南高新技术企业认定标准..."
            class="w-full pl-6 pr-16 py-5 bg-transparent border-none focus:ring-0 resize-none text-slate-700 placeholder-slate-400 text-[15px] leading-relaxed min-h-[60px] outline-none" 
            rows="1"
            ref="textareaRef"
            :style="{ height: textareaHeight }"
          ></textarea>
          
          <div class="flex justify-between items-center px-4 pb-3">
             <div class="flex gap-2">
                <button class="p-2 rounded-lg hover:bg-slate-100 text-slate-400 hover:text-teal-600 transition-colors" title="上传文件（演示）">
                   <svg class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15.172 7l-6.586 6.586a2 2 0 102.828 2.828l6.414-6.586a4 4 0 00-5.656-5.656l-6.415 6.585a6 6 0 108.486 8.486L20.5 13" /></svg>
                </button>
             </div>

             <button 
              @click="sendMessage"
              :disabled="!userInput.trim() || isThinking"
              class="relative group/btn p-2.5 rounded-xl bg-slate-900 text-white hover:bg-teal-600 disabled:bg-slate-200 disabled:text-slate-400 disabled:cursor-not-allowed transition-all duration-300 shadow-lg shadow-slate-900/20 hover:shadow-teal-500/30 hover:scale-105 active:scale-95 flex items-center justify-center overflow-hidden"
            >
              <div class="absolute inset-0 bg-white/20 translate-y-full group-hover/btn:translate-y-0 transition-transform duration-300"></div>
              <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 relative z-10" viewBox="0 0 20 20" fill="currentColor">
                <path d="M10.894 2.553a1 1 0 00-1.788 0l-7 14a1 1 0 001.169 1.409l5-1.429A1 1 0 009 15.571V11a1 1 0 112 0v4.571a1 1 0 00.725.962l5 1.428a1 1 0 001.17-1.408l-7-14z" />
              </svg>
            </button>
          </div>
        </div>
        
        <div class="mt-4 flex justify-center items-center gap-3 text-[10px] text-slate-400 font-medium uppercase tracking-widest opacity-60">
           <span>Powered by SD-Government LLM</span>
           <span class="w-1 h-1 rounded-full bg-slate-300"></span>
           <span>V 2.0.1 Beta</span>
        </div>
      </div>
    </footer>
  </div>
</template>

<script setup>
import { ref, nextTick, watch, computed } from 'vue';
import MarkdownIt from 'markdown-it';
import hljs from 'highlight.js';
import 'highlight.js/styles/atom-one-light.css'; 

const quickActions = [
  { 
    path: 'M9.75 17L9 20l-1 1h8l-1-1-.75-3M3 13h18M5 17h14a2 2 0 002-2V5a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z', 
    color: 'bg-blue-500', 
    title: '消费品以旧换新', 
    desc: '电脑/家电补贴标准查询', 
    text: '济南买电脑有什么补贴政策？' 
  },
  { 
    path: 'M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z', 
    color: 'bg-teal-500', 
    title: '政策对比分析', 
    desc: '跨城市/跨年度差异比对', 
    text: '青岛和济南的汽车补贴政策对比' 
  },
  { 
    path: 'M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z', 
    color: 'bg-indigo-500', 
    title: '申报材料清单', 
    desc: '办事指南与证件下载', 
    text: '申领家电补贴需要什么材料？' 
  },
  { 
    path: 'M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-3m-6 3v-3m6-3V7m6 10v-3m-6 3v-3m6-3V7', 
    color: 'bg-orange-500', 
    title: '企业推荐与招商', 
    desc: '优质企业名录/选址', 
    text: '推荐几个济南的优质餐饮企业' 
  },
];

const API_ENDPOINT = 'http://146.56.198.222:8001/query';

// --- 状态定义 ---
const messages = ref([]);
const userInput = ref('');
const isThinking = ref(false);
const isStreaming = ref(false);
const messagesContainer = ref(null);
const textareaRef = ref(null);

// --- Markdown 配置 ---
const md = new MarkdownIt({
  html: false,
  linkify: true,
  highlight: function (str, lang) {
    if (lang && hljs.getLanguage(lang)) {
      try {
        return `<pre class="hljs p-4 rounded-xl text-sm bg-slate-50 border border-slate-100 overflow-x-auto my-3 relative group"><div class="absolute top-2 right-2 text-[10px] text-slate-300 uppercase select-none">${lang}</div><code>` +
               hljs.highlight(str, { language: lang, ignoreIllegals: true }).value +
               `</code></pre>`;
      } catch (__) {}
    }
    return `<pre class="bg-slate-50 text-slate-700 p-4 rounded-xl text-sm border border-slate-100 overflow-x-auto my-3"><code>` + md.utils.escapeHtml(str) + `</code></pre>`;
  }
});

const renderMarkdown = (text) => md.render(text);

// --- 自动增高 Textarea ---
const textareaHeight = computed(() => 'auto');
watch(userInput, () => {
  nextTick(() => {
    if (textareaRef.value) {
      textareaRef.value.style.height = 'auto';
      textareaRef.value.style.height = Math.min(textareaRef.value.scrollHeight, 150) + 'px';
    }
  });
});

// --- 核心逻辑 ---
const scrollToBottom = async () => {
  await nextTick();
  if (messagesContainer.value) {
    messagesContainer.value.scrollTop = messagesContainer.value.scrollHeight;
  }
};

const setInput = (text) => {
  userInput.value = text;
  if (textareaRef.value) {
    textareaRef.value.focus();
  }
};

const handleEnter = (event) => {
  if (!event.shiftKey) {
    sendMessage();
  }
};

const clearHistory = () => {
  messages.value = [];
  userInput.value = '';
};

// 模拟 AI 逐字输出
const simulateStreamResponse = async (fullText, meta = {}) => {
  isStreaming.value = true;
  const messageIndex = messages.value.length;
  messages.value.push({ 
    role: 'ai', 
    content: '', 
    done: false,
    showChart: false, 
    showChart2: false, 
    ...meta 
  });
  
  let currentText = '';
  const chunkSize = 3;
  
  for (let i = 0; i < fullText.length; i += chunkSize) {
    await new Promise((resolve) => setTimeout(resolve, 10 + Math.random() * 15));
    currentText += fullText.slice(i, i + chunkSize);
    messages.value[messageIndex].content = currentText;
    scrollToBottom();
  }
  
  messages.value[messageIndex].done = true; 
  isStreaming.value = false;

  if (meta.chartUrl) {
    await new Promise((resolve) => setTimeout(resolve, 800));
    messages.value[messageIndex].showChart = true;
    scrollToBottom();
  }
  
  if (meta.chartUrl2) {
    await new Promise((resolve) => setTimeout(resolve, 1500));
    messages.value[messageIndex].showChart2 = true;
    scrollToBottom();
  }
};

const sendMessage = async () => {
  const text = userInput.value.trim();
  if (!text || isThinking.value) return;

  messages.value.push({ role: 'user', content: text });
  userInput.value = '';
  if (textareaRef.value) {
    textareaRef.value.style.height = 'auto';
  }
  scrollToBottom();

  isThinking.value = true;

  try {
    const response = await fetch(API_ENDPOINT, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ query: text }),
    });

    if (!response.ok) throw new Error(`Failed to fetch`);

    const payload = await response.json();
    const finalAnswer = payload.final_answer || payload.result?.final_answer || '暂无回答内容。';
    
    let references = [];
    if (payload.kb_citations) {
      references = payload.kb_citations.map((cite, idx) => ({
        id: `${idx + 1}`,
        text: cite.text || cite.content || JSON.stringify(cite)
      }));
    }
    
    let chartUrl = null;
    let chartUrl2 = null;
    if (payload.mcp_enhancements?.demo_chart) {
      chartUrl = payload.mcp_enhancements.demo_chart.chart_url;
    }
    if (text.includes('餐饮') && text.includes('招商')) {
      chartUrl2 = '/demo-charts/map-chart.png';
    }
    
    await simulateStreamResponse(finalAnswer, { references, chartUrl, chartUrl2 });
  } catch (error) {
    await simulateStreamResponse(`**系统提示**：当前服务暂时繁忙，请稍后重试。\n> 错误信息：${error.message}`);
  } finally {
    isThinking.value = false;
  }
};
</script>

<style>
/* 滚动条美化 */
.custom-scrollbar::-webkit-scrollbar {
  width: 6px;
}
.custom-scrollbar::-webkit-scrollbar-track {
  background: transparent;
}
.custom-scrollbar::-webkit-scrollbar-thumb {
  background-color: rgba(148, 163, 184, 0.3);
  border-radius: 20px;
}
.custom-scrollbar::-webkit-scrollbar-thumb:hover {
  background-color: rgba(148, 163, 184, 0.5);
}

/* 动画定义 */
@keyframes float-slow {
  0%, 100% { transform: translate(0, 0) scale(1); }
  50% { transform: translate(-20px, -20px) scale(1.05); }
}
.animate-float-slow { animation: float-slow 15s ease-in-out infinite; }

@keyframes float-delayed {
  0%, 100% { transform: translate(0, 0) scale(1); }
  50% { transform: translate(20px, 20px) scale(1.05); }
}
.animate-float-delayed { animation: float-delayed 18s ease-in-out infinite; }

@keyframes fade-in-up {
  from { opacity: 0; transform: translateY(30px); }
  to { opacity: 1; transform: translateY(0); }
}
.animate-fade-in-up { animation: fade-in-up 0.8s cubic-bezier(0.2, 0.8, 0.2, 1) forwards; }

@keyframes slide-in {
  from { opacity: 0; transform: translateY(10px) scale(0.98); }
  to { opacity: 1; transform: translateY(0) scale(1); }
}
.animate-slide-in { animation: slide-in 0.4s cubic-bezier(0.2, 0.8, 0.2, 1) forwards; }

/* Markdown 样式 */
.markdown-body h1, .markdown-body h2, .markdown-body h3 {
  color: #1e293b; font-weight: 800; margin-top: 1.5em; margin-bottom: 0.8em; line-height: 1.3;
}
.markdown-body h3 {
  font-size: 1.1em; border-left: 4px solid #0f766e; padding-left: 1em;
  background: linear-gradient(to right, #f0fdfa, transparent);
  padding-top: 0.2em; padding-bottom: 0.2em; border-radius: 0 4px 4px 0;
}
.markdown-body p { margin-bottom: 1.2em; text-align: justify; line-height: 1.8; color: #334155; }
.markdown-body ul { list-style-type: disc; padding-left: 1.5em; margin-bottom: 1.2em; }
.markdown-body li { margin-bottom: 0.5em; color: #475569; }
.markdown-body strong { color: #0d9488; font-weight: 700; }
.markdown-body table { width: 100%; border-collapse: separate; border-spacing: 0; margin: 1.5em 0; font-size: 0.9em; border: 1px solid #e2e8f0; border-radius: 8px; overflow: hidden; box-shadow: 0 4px 6px -1px rgba(0,0,0,0.05); }
.markdown-body th { background-color: #f8fafc; font-weight: 700; text-align: left; padding: 1em; border-bottom: 1px solid #e2e8f0; color: #475569; }
.markdown-body td { padding: 1em; border-bottom: 1px solid #e2e8f0; color: #334155; background-color: #fff; }
.markdown-body tr:last-child td { border-bottom: none; }
</style>