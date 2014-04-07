---
layout: post
title: "怎样把PDF文件在WinForm窗口中显示出来 "
description: ""
category:  .net控件相关
tags: [" .net控件相关"]
---

Steps to create the C# version
1. Create a windows form application by using VS
2. Make the Acrobat control available in the toolbox of VS
Tools->Add/Remove Toolbox Items: turn on "Adobe Acrobat 7.0 Browser Document" in COM Components tab. You will see it in the Toolbox's General tab.
3. Drag this control to the form. you get:
private AxAcroPDFLib.AxAcroPDF axAcroPDF1;
4. Load a pdf file and make it show up.
axAcroPDF1.LoadFile("mypdf.pdf");
axAcroPDF1.Show();
5. Compile and run, you will see the document showing up inside of the control.
Here is the code piece.
public class Form1 : System.Windows.Forms.Form
{
private AxAcroPDFLib.AxAcroPDF axAcroPDF1;
/// 
/// Required designer variable.
/// 
private System.ComponentModel.Container components = null;
public Form1()
{
//
// Required for Windows Form Designer support
//
InitializeComponent();
axAcroPDF1.LoadFile("mypdf.pdf");
axAcroPDF1.Show();
}
