LECTURE 16
--------------------

API - Application Programming Interface
--------------------------------------------

Its set of rules that allows the programs to talk to each other 
The developer create the API on the server and allows the client to talk to it.

REST - Representational state Transfer
----------------------------------
    A set of Constratints to be used for creating we Services

    GET
    PUT
    POST
    DELETE
    Basic request in the REST API



    SPRING BOOT DEV TOOLS
    -------------------------
    Dev tool - Developer tool 
    Continiously monitors the class path and whever finds change it will restart the AppliCation

    Create A new Project 
    Add dependencied
    -- Spring Web
    -- Spring Dev tools
-------------------------------------------------------------------------------
package com.dev.tool;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class BootdevToolApplication {

	public static void main(String[] args) {
		SpringApplication.run(BootdevToolApplication.class, args);
	}

}
--------------------------------------------------------------

CONTROLLER CLASS

package com.dev.tool.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class TestController {
	
	@RequestMapping("/test")
	@ResponseBody   // when we have to send whaterver we are returning and we dont have a view.
	public String test() {
		int a =10;
		int b = 200;
		return "This is just for testing puspose "+(a+b);
	}

}

-------------------------------------------------------------------------------------------------------
    
