---
layout: post_w
title: "codereview.stackexchange questions"
tags: [code] 
categories: [code review]
---

# codereview.stackexchange

* [Remove-Last-Comma-Problem](http://codereview.stackexchange.com/questions/1973/remove-last-comma-problem)   

		List<String> paramList = new ArrayList<String>( );
		paramList.add( "param1" );
		paramList.add( "param2" );

		StringBuilder result = new StringBuilder();
		for ( String p : paramList )
		{
		  result.append( p ).append( ", " );
		}

		String withoutLastComma = result.substring( 0, result.length( ) - ", ".length( ) );
		System.err.println( withoutLastComma );

	有更好的办法来去掉最后一个逗号吗?

		StringUtils.join() //apache commons-lang api

