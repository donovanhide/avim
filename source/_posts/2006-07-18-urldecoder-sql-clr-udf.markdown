---
layout: post
title: "URLDecoder SQL CLR UDF"
date: 2006-07-18 09:03:13
comments: true
categories:
---

Annoyingly the SQL CLR engine does not allow the use of the System.Web assembly and consequently you cannot use the very helpful HttpServerUtility.UrlDecode member in your User Defined Functions. Here is a quick hack which does something similar. Useful if you are in a rush!

	using System;
	using System.Collections.Specialized;
	using System.Data;
	using System.Data.SqlClient;
	using System.Data.SqlTypes;
	using Microsoft.SqlServer.Server;

	public partial class UserDefinedFunctions
	{
	    [Microsoft.SqlServer.Server.SqlFunction]
	    public static SqlString URLDecode(SqlString URL)
	    {
	       string[]Encoders=new string[]{\" ","%20","!","%21","\\"","%22","#","%23","$","%24",
	           "%","%25","&","%26","'","%27","(","%28",")","%29","*","%2a","+","%2b",",","%2c",
	           "-","%2d",".","%2e","/","%2f",":","%3a",";","%3b","<","%3c","=","%3d",">","%3e",
	           "?","%3f","@","%40","[","%5b","\\\\","%5c","]","%5d","^","%5e","_","%5f","`","%60",
	           "{","%7b","|","%7c","}","%7d","~","%7e","","%7f","€","%80","","%81","‚","%82",
	           "ƒ","%83","„","%84","…","%85","†","%86","‡","%87","ˆ","%88","‰","%89","Š","%8a",
	           "‹","%8b","Œ","%8c","","%8d","Ž","%8e","","%8f","","%90","‘","%91","’","%92",
	           "“","%93","”","%94","•","%95","–","%96","—","%97","˜","%98","™","%99","š",
	           "%9a","›","%9b","œ","%9c","","%9d","ž","%9e","Ÿ","%9f","","%a0","¡","%a1",
	           "¢","%a2","£","%a3","","%a4","¥","%a5","|","%a6","§","%a7","¨","%a8","©","%a9",
	           "ª","%aa","«","%ab","¬","%ac","¯","%ad","®","%ae","¯","%af","°","%b0","±",
	           "%b1","²","%b2","³","%b3","´","%b4","µ","%b5","¶","%b6","·","%b7","¸","%b8",
	           "¹","%b9","º","%ba","»","%bb","¼","%bc","½","%bd","¾","%be","¿","%bf","À",
	           "%c0","Á","%c1","Â","%c2","Ã","%c3","Ä","%c4","Å","%c5","Æ","%c6","Ç","%c7",
	           "È","%c8","É","%c9","Ê","%ca","Ë","%cb","Ì","%cc","Í","%cd","Î","%ce","Ï",
	           "%cf","Ð","%d0","Ñ","%d1","Ò","%d2","Ó","%d3","Ô","%d4","Õ","%d5","Ö","%d6",
	           "","%d7","Ø","%d8","Ù","%d9","Ú","%da","Û","%db","Ü","%dc","Ý","%dd","Þ",
	           "%de","ß","%df","à","%e0","á","%e1","â","%e2","ã","%e3","ä","%e4","å","%e5",
	           "æ","%e6","ç","%e7","è","%e8","é","%e9","ê","%ea","ë","%eb","ì","%ec","í",
	           "%ed","î","%ee","ï","%ef","ð","%f0","ñ","%f1","ò","%f2","ó","%f3","ô","%f4",
	           "õ","%f5","ö","%f6","÷","%f7","ø","%f8","ù","%f9","ú","%fa","û","%fb","ü",
	           "%fc","ý","%fd","þ","%fe","ÿ","%ff"};
	        string URLString = URL.ToString();
	        for (int i =1;i<Encoders.Length;i+=2)
	        {
	            if (URLString.Contains(Encoders[i]))
	            {
	                URLString = URLString.Replace(Encoders[i],Encoders[i-1]);
	            }
	            else if (URLString.Contains(Encoders[i].ToUpper()))
	            {
	                URLString = URLString.Replace(Encoders[i].ToUpper(),Encoders[i-1]);
	            }
	        }
	        return new SqlString(URLString.ToString());
	    }
	};