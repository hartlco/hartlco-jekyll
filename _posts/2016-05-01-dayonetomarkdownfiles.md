---
id: 208
title: DayOneToMarkdownFiles
date: 2016-05-01T10:59:56+00:00
author: hartlco
layout: post
guid: https://hartl.co/?p=208
permalink: /2016/05/01/dayonetomarkdownfiles/
link:
  - https://github.com/hartlco/DayOneToMarkdownFiles
categories:
  - Allgemein
---
Seit dem Erscheinen von Day One 2 hat es mit immer mehr verärgert, dass der Dropbox-Sync weggefallen ist, und der hauseigene Service verwendet werden muss. Ich verstehe, warum Day One diesen Schritt ging, allerdings gefällt es mir nicht, dass meine persönlichsten Notizen auf einem fremden Server liegen und ich nicht direkt Zugriff auf die Dateien habe.

Ich habe mir somit ein kleines [Swift Tool](https://github.com/hartlco/DayOneToMarkdownFiles) geschrieben, welches die im JSON-Format exportierten Day One Einträge in Markdown-Dateien ablegt. Die Bilder werden dabei mit sinnvolleren Dateinamen umbenannt und als Markdown-Bild verlinkt. Ich übernehme lediglich die Wetter- und Ortsinformationen mit in die Markdown-Datei, da mich die anderen Informationen nicht wirklich interessieren.

Ich verwende jetzt Editorial/Ulysses und einen Ordner in Dropbox zum Führen des Tagebuchs.

[Quelle](https://github.com/hartlco/DayOneToMarkdownFiles)