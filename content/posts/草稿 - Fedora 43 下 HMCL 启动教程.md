---
title: Fedora 43 下 HMCL 的安装指南
description: 
tags: [HMCL, Linux]
date: 2025-11-22T10:45:00+08:00
author: NWMA_FYWF
draft: true
---

# Fedora 43 下安装 `HMCL` 

> data: 2025/11/22 - 10:45 | by: NMWA_FYWF

> [!CAUTION]
>
> ## 注意! ! ! 
>
> 直接 `java -jar HMCL-3.7.5.jar` 出现如下错误
>
> ```bash
> dev@192:~/.local/opt/hmcl$ java -jar HMCL-3.7.5.jar 
> [09:47:11] [@.util.SelfDependencyPatcher.patch/INFO] Missing JavaFX dependencies, attempting to patch in missing classes
> Exception in thread "main" java.awt.HeadlessException: 
> No X11 DISPLAY variable was set,
> or no headful library support was found,
> but this program performed an operation which requires it.
> 	at java.desktop/java.awt.GraphicsEnvironment.checkHeadless(GraphicsEnvironment.java:158)
> 	at java.desktop/java.awt.Window.<init>(Window.java:518)
> 	at java.desktop/java.awt.Frame.<init>(Frame.java:428)
> 	at java.desktop/java.awt.Frame.<init>(Frame.java:393)
> 	at java.desktop/javax.swing.SwingUtilities$SharedOwnerFrame.<init>(SwingUtilities.java:1919)
> 	at java.desktop/javax.swing.SwingUtilities.getSharedOwnerFrame(SwingUtilities.java:1987)
> 	at java.desktop/javax.swing.JDialog.<init>(JDialog.java:276)
> 	at java.desktop/javax.swing.JDialog.<init>(JDialog.java:210)
> 	at java.desktop/javax.swing.JDialog.<init>(JDialog.java:158)
> 	at org.jackhuang.hmcl.util.SelfDependencyPatcher$ProgressFrame.<init>(SelfDependencyPatcher.java:396)
> 	at org.jackhuang.hmcl.util.SelfDependencyPatcher.fetchDependencies(SelfDependencyPatcher.java:277)
> 	at org.jackhuang.hmcl.util.SelfDependencyPatcher.patch(SelfDependencyPatcher.java:185)
> 	at org.jackhuang.hmcl.EntryPoint.checkJavaFX(EntryPoint.java:185)
> 	at org.jackhuang.hmcl.EntryPoint.main(EntryPoint.java:55)
> 	at org.jackhuang.hmcl.Main.main(Main.java:78)
> ^Cdev@192:~/.local/opt/hmcl$ 
> ```
>
> 这个 `java.awt.HeadlessException` 错误表明 Java 无法找到图形显示环境
>
> **Fedora 43 已全面转向 Wayland（不再默认支持 `X11`）**，同时 **`JavaFX` 包在新版 Fedora 中发生了重大变化** —— **`java-\*-openjdk-javafx` 包已被移除**，因为上游 `OpenJDK` 不再包含 `JavaFX`，而 Fedora 也停止打包它
>
> Fedora 自 **Fedora 38 起不再打包 `JavaFX`**，因为 Oracle 不再允许其与 `OpenJDK` 一起分发。 
>

---

## 正确解决方案：手动提供  `JavaFX`

由于系统无法通过 `dnf` 安装 `JavaFX`，你需要 **手动下载 `JavaFX SDK`** 并运行 `HMCL` 时显式指定模块路径。

### 1. 下载 `JavaFX for Linux (x64)`

下载后解压，例如：

```
cd ~/Downloads
wget https://download2.gluonhq.com/openjfx/21.0.2/openjfx-21.0.2_linux-x64_bin-sdk.zip
unzip openjfx-21.0.2_linux-x64_bin-sdk.zip
mv javafx-sdk-21.0.2 ~/javafx-sdk
```

### 2. 在 Wayland 下启用 `X11`兼容（重要！）

虽然 GNOME 默认用 Wayland，但 **`Java AWT/Swing` 在 `Wayland` 下支持不完整**，建议强制使用 `X11` 后端：

```
export GDK_BACKEND=x11
export _JAVA_AWT_WM_NONREPARENTING=1  # 避免窗口管理器问题（可选）
```

### 3. 使用 JavaFX 模块运行 HMCL

```
cd ~/games
java \
  --module-path ~/javafx-sdk/lib \
  --add-modules javafx.controls,javafx.fxml,javafx.swing,javafx.media \
  -jar HMCL-3.7.5.jar
```

<!--这样 HMCL 就能正常加载 JavaFX 并显示界面-->

```
dev@192:~/.local/opt/hmcl$ java --module-path ~/javafx-sdk/lib --add-modules javafx.controls,javafx.fxml,javafx.swing,javafx.media -jar HMCL-3.7.5.jar 
[10:03:10] [@.Launcher.main/INFO] *** HMCL 3.7.5 ***
[10:03:10] [@.Launcher.main/INFO] Operating System: Fedora Linux 43 (Workstation Edition) (Linux 6.17.8-300.fc43.x86_64)
[10:03:10] [@.Launcher.main/INFO] System Architecture: x86-64
[10:03:10] [@.Launcher.main/INFO] Native Encoding: UTF-8
[10:03:10] [@.Launcher.main/INFO] JNU Encoding: UTF-8
[10:03:10] [@.Launcher.main/INFO] Java Architecture: x86-64
[10:03:10] [@.Launcher.main/INFO] Java Version: 25.0.1, Red Hat, Inc.
[10:03:10] [@.Launcher.main/INFO] Java VM Version: OpenJDK 64-Bit Server VM (mixed mode, sharing), Red Hat, Inc.
[10:03:10] [@.Launcher.main/INFO] Java Home: /usr/lib/jvm/java-25-openjdk
[10:03:10] [@.Launcher.main/INFO] Current Directory: /home/dev/.local/opt/hmcl
[10:03:10] [@.Launcher.main/INFO] HMCL Global Directory: /home/dev/.local/share/hmcl
[10:03:10] [@.Launcher.main/INFO] HMCL Current Directory: /home/dev/.local/opt/hmcl/.hmcl
[10:03:10] [@.Launcher.main/INFO] HMCL Jar Path: /home/dev/.local/opt/hmcl/HMCL-3.7.5.jar
[10:03:10] [@.Launcher.main/INFO] HMCL Log File: /home/dev/.local/opt/hmcl/.hmcl/logs/2025-11-22T10-03-10.log
[10:03:10] [@.Launcher.main/INFO] JVM Max Memory: 4480 MiB
[10:03:10] [@.Launcher.main/INFO] Metaspace: 3.89 MiB
[10:03:10] [@.Launcher.main/INFO] Native Backend: None
[10:03:10] [@.Launcher.main/INFO] XDG Session Type: wayland
[10:03:10] [@.Launcher.main/INFO] XDG Current Desktop: GNOME
[10:03:10] [@.util.platform.SystemInfo.initialize/INFO] System Info:

 - CPU: AMD Ryzen 5 3400G (4 Cores / 8 Threads)
 - GPU: AMD Radeon Vega 11 Graphics [Integrated]
 - Memory: 3.46 GiB / 17.5 GiB (19%)
   [10:03:11] [@.Launcher.start/INFO] JavaFX Version: 21.0.2+5
   [10:03:11] [@.Launcher.start/INFO] Prism Pipeline: com.sun.prism.es2.ES2Pipeline
   [10:03:11] [@.Launcher.start/INFO] Dark Mode: false
   [10:03:11] [@.Launcher.start/INFO] Reduced Motion: false
   [10:03:11] [@.setting.ConfigHolder.init/INFO] Config location: /home/dev/.local/opt/hmcl/.hmcl/hmcl.json
   [10:03:11] [@.setting.ConfigHolder.loadGlobalConfig/INFO] Creating an empty global config
   [10:03:11] [@.setting.ConfigHolder.init/INFO] Creating config file /home/dev/.local/opt/hmcl/.hmcl/hmcl.json
   [10:03:11] [@.task.AsyncTaskExecutor.lambda$executeNormalTask$19/TRACE] Executing task: org.jackhuang.hmcl.game.GameRepository.refreshVersionsAsync(GameRepository.java:91)
   [10:03:11] [@.task.AsyncTaskExecutor.lambda$executeNormalTask$19/TRACE] Executing task: org.jackhuang.hmcl.game.GameRepository.refreshVersionsAsync(GameRepository.java:91)
   [10:03:11] [@.event.EventBus.fireEvent/INFO] RefreshingVersionsEvent [source=HMCLGameRepository [versions=null, baseDirectory=.minecraft]] gets fired
   [10:03:11] [@.event.EventBus.fireEvent/INFO] RefreshingVersionsEvent [source=HMCLGameRepository [versions=null, baseDirectory=.minecraft]] gets fired
   [10:03:11] [@.event.EventBus.fireEvent/INFO] RefreshedVersionsEvent [source=HMCLGameRepository [versions=[], baseDirectory=.minecraft]] gets fired
   [10:03:11] [@.event.EventBus.fireEvent/INFO] RefreshedVersionsEvent [source=HMCLGameRepository [versions=[], baseDirectory=.minecraft]] gets fired
   [10:03:11] [@.task.AsyncTaskExecutor.lambda$executeNormalTask$29/TRACE] Task finished: org.jackhuang.hmcl.game.GameRepository.refreshVersionsAsync(GameRepository.java:91)
   [10:03:11] [@.task.AsyncTaskExecutor.lambda$executeNormalTask$29/TRACE] Task finished: org.jackhuang.hmcl.game.GameRepository.refreshVersionsAsync(GameRepository.java:91)
   [10:03:11] [@.ui.Controllers.initialize/INFO] Start initializing application
   [10:03:11] [@.util.FileSaver.saveSync/INFO] Saving file /home/dev/.local/opt/hmcl/.hmcl/hmcl.json
   [10:03:11] [@.java.JavaManager.searchPotentialJavaExecutables/TRACE] Finished Java lookup, found 1
 - JRE 25.0.1 (x86-64, Red Hat, Inc.): /usr/lib/jvm/java-25-openjdk/bin/java
   [10:03:11] [@.setting.FontManager.findByFcMatch/INFO] Load font file: /usr/share/fonts/google-noto-sans-cjk-vf-fonts/NotoSansCJK-VF.ttc
   [10:03:12] [@.setting.FontManager.<clinit>/INFO] Font: Noto Sans CJK SC
   [10:03:12] [@.auth.authlibinjector.AuthlibInjectorServer.refreshMetadata/INFO] authlib-injector server metadata refreshed: https://littleskin.cn/api/yggdrasil/
   [10:03:12] [@.ui.construct.JFXDialogPane.push/INFO] JFXDialogPane@7f64efdf [JFXDialogLayout@1db3cfeb[styleClass=jfx-dialog-layout]]
   [10:03:12] [@.util.FileSaver.saveSync/INFO] Saving file /home/dev/.local/opt/hmcl/.hmcl/hmcl.json
   [10:03:12] [@.upgrade.IntegrityChecker.isSelfVerified/INFO] Successfully verified current JAR
   [10:03:13] [@.upgrade.UpdateChecker.lambda$requestCheckUpdate$2/INFO] Latest version (STABLE, preview=false) is [3.7.5 from http://mirrors.cloud.tencent.com/nexus/repository/maven-public/org/glavo/hmcl/hmcl-stable/3.7.5/hmcl-stable-3.7.5.jar]
   [10:03:14] [@.util.FileSaver.saveSync/INFO] Saving file /home/dev/.local/share/hmcl/config.json
   [10:03:14] [@.ui.construct.JFXDialogPane.pop/INFO] JFXDialogPane@7f64efdf [], removed: true, object: JFXDialogLayout@1db3cfeb[styleClass=jfx-dialog-layout]
   [10:03:22] [@.Launcher.stopApplication/INFO] Stopping application.
   at java.base/java.lang.Thread.getStackTrace(Thread.java:2193)
   at org.jackhuang.hmcl.Launcher.stopApplication(Launcher.java:286)
   at org.jackhuang.hmcl.ui.decorator.DecoratorController.lambda$new$1(DecoratorController.java:108)
   at org.jackhuang.hmcl.ui.decorator.Decorator.close(Decorator.java:302)
   at org.jackhuang.hmcl.ui.decorator.DecoratorSkin.lambda$new$7(DecoratorSkin.java:248)
   at javafx.base@21.0.2/com.sun.javafx.event.CompositeEventHandler.dispatchBubblingEvent(CompositeEventHandler.java:86)
   at javafx.base@21.0.2/com.sun.javafx.event.EventHandlerManager.dispatchBubblingEvent(EventHandlerManager.java:232)
   at javafx.base@21.0.2/com.sun.javafx.event.EventHandlerManager.dispatchBubblingEvent(EventHandlerManager.java:189)
   at javafx.base@21.0.2/com.sun.javafx.event.CompositeEventDispatcher.dispatchBubblingEvent(CompositeEventDispatcher.java:59)
   at javafx.base@21.0.2/com.sun.javafx.event.BasicEventDispatcher.dispatchEvent(BasicEventDispatcher.java:58)
   at javafx.base@21.0.2/com.sun.javafx.event.EventDispatchChainImpl.dispatchEvent(EventDispatchChainImpl.java:114)
   at javafx.base@21.0.2/com.sun.javafx.event.BasicEventDispatcher.dispatchEvent(BasicEventDispatcher.java:56)
   at javafx.base@21.0.2/com.sun.javafx.event.EventDispatchChainImpl.dispatchEvent(EventDispatchChainImpl.java:114)
   at javafx.base@21.0.2/com.sun.javafx.event.BasicEventDispatcher.dispatchEvent(BasicEventDispatcher.java:56)
   at javafx.base@21.0.2/com.sun.javafx.event.EventDispatchChainImpl.dispatchEvent(EventDispatchChainImpl.java:114)
   at javafx.base@21.0.2/com.sun.javafx.event.BasicEventDispatcher.dispatchEvent(BasicEventDispatcher.java:56)
   at javafx.base@21.0.2/com.sun.javafx.event.EventDispatchChainImpl.dispatchEvent(EventDispatchChainImpl.java:114)
   at javafx.base@21.0.2/com.sun.javafx.event.BasicEventDispatcher.dispatchEvent(BasicEventDispatcher.java:56)
   at javafx.base@21.0.2/com.sun.javafx.event.EventDispatchChainImpl.dispatchEvent(EventDispatchChainImpl.java:114)
   at javafx.base@21.0.2/com.sun.javafx.event.EventUtil.fireEventImpl(EventUtil.java:74)
   at javafx.base@21.0.2/com.sun.javafx.event.EventUtil.fireEvent(EventUtil.java:49)
   at javafx.base@21.0.2/javafx.event.Event.fireEvent(Event.java:198)
   at javafx.graphics@21.0.2/javafx.scene.Node.fireEvent(Node.java:8875)
   at javafx.controls@21.0.2/javafx.scene.control.Button.fire(Button.java:203)
   at javafx.controls@21.0.2/com.sun.javafx.scene.control.behavior.ButtonBehavior.mouseReleased(ButtonBehavior.java:207)
   at javafx.controls@21.0.2/com.sun.javafx.scene.control.inputmap.InputMap.handle(InputMap.java:274)
   at javafx.base@21.0.2/com.sun.javafx.event.CompositeEventHandler$NormalEventHandlerRecord.handleBubblingEvent(CompositeEventHandler.java:247)
   at javafx.base@21.0.2/com.sun.javafx.event.CompositeEventHandler.dispatchBubblingEvent(CompositeEventHandler.java:80)
   at javafx.base@21.0.2/com.sun.javafx.event.EventHandlerManager.dispatchBubblingEvent(EventHandlerManager.java:232)
   at javafx.base@21.0.2/com.sun.javafx.event.EventHandlerManager.dispatchBubblingEvent(EventHandlerManager.java:189)
   at javafx.base@21.0.2/com.sun.javafx.event.CompositeEventDispatcher.dispatchBubblingEvent(CompositeEventDispatcher.java:59)
   at javafx.base@21.0.2/com.sun.javafx.event.BasicEventDispatcher.dispatchEvent(BasicEventDispatcher.java:58)
   at javafx.base@21.0.2/com.sun.javafx.event.EventDispatchChainImpl.dispatchEvent(EventDispatchChainImpl.java:114)
   at javafx.base@21.0.2/com.sun.javafx.event.BasicEventDispatcher.dispatchEvent(BasicEventDispatcher.java:56)
   at javafx.base@21.0.2/com.sun.javafx.event.EventDispatchChainImpl.dispatchEvent(EventDispatchChainImpl.java:114)
   at javafx.base@21.0.2/com.sun.javafx.event.BasicEventDispatcher.dispatchEvent(BasicEventDispatcher.java:56)
   at javafx.base@21.0.2/com.sun.javafx.event.EventDispatchChainImpl.dispatchEvent(EventDispatchChainImpl.java:114)
   at javafx.base@21.0.2/com.sun.javafx.event.BasicEventDispatcher.dispatchEvent(BasicEventDispatcher.java:56)
   at javafx.base@21.0.2/com.sun.javafx.event.EventDispatchChainImpl.dispatchEvent(EventDispatchChainImpl.java:114)
   at javafx.base@21.0.2/com.sun.javafx.event.BasicEventDispatcher.dispatchEvent(BasicEventDispatcher.java:56)
   at javafx.base@21.0.2/com.sun.javafx.event.EventDispatchChainImpl.dispatchEvent(EventDispatchChainImpl.java:114)
   at javafx.base@21.0.2/com.sun.javafx.event.EventUtil.fireEventImpl(EventUtil.java:74)
   at javafx.base@21.0.2/com.sun.javafx.event.EventUtil.fireEvent(EventUtil.java:54)
   at javafx.base@21.0.2/javafx.event.Event.fireEvent(Event.java:198)
   at javafx.graphics@21.0.2/javafx.scene.Scene$MouseHandler.process(Scene.java:3984)
   at javafx.graphics@21.0.2/javafx.scene.Scene.processMouseEvent(Scene.java:1890)
   at javafx.graphics@21.0.2/javafx.scene.Scene$ScenePeerListener.mouseEvent(Scene.java:2708)
   at javafx.graphics@21.0.2/com.sun.javafx.tk.quantum.GlassViewEventHandler$MouseEventNotification.run(GlassViewEventHandler.java:411)
   at javafx.graphics@21.0.2/com.sun.javafx.tk.quantum.GlassViewEventHandler$MouseEventNotification.run(GlassViewEventHandler.java:301)
   at java.base/java.security.AccessController.doPrivileged(AccessController.java:138)
   at javafx.graphics@21.0.2/com.sun.javafx.tk.quantum.GlassViewEventHandler.lambda$handleMouseEvent$2(GlassViewEventHandler.java:450)
   at javafx.graphics@21.0.2/com.sun.javafx.tk.quantum.QuantumToolkit.runWithoutRenderLock(QuantumToolkit.java:424)
   at javafx.graphics@21.0.2/com.sun.javafx.tk.quantum.GlassViewEventHandler.handleMouseEvent(GlassViewEventHandler.java:449)
   at javafx.graphics@21.0.2/com.sun.glass.ui.View.handleMouseEvent(View.java:551)
   at javafx.graphics@21.0.2/com.sun.glass.ui.View.notifyMouse(View.java:937)
   at javafx.graphics@21.0.2/com.sun.glass.ui.gtk.GtkApplication._runLoop(Native Method)
   at javafx.graphics@21.0.2/com.sun.glass.ui.gtk.GtkApplication.lambda$runLoop$10(GtkApplication.java:263)
   at java.base/java.lang.Thread.run(Thread.java:1474)

[10:03:22] [@.task.Schedulers.shutdown/INFO] Shutting down executor services.
[10:03:22] [@.util.FileSaver.saveSync/INFO] Saving file /home/dev/.local/opt/hmcl/.hmcl/hmcl.json
dev@192:~/.local/opt/hmcl$ 
```

2025年11月22日 10:49:20
 