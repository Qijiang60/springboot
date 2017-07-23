URL
spring boot get和post请求,以及requestbody为json串时候的处理
GET、POST方式提时， 根据request header Content-Type的值来判断:
    application/x-www-form-urlencoded， 可选（即非必须，因为这种情况的数据@RequestParam, @ModelAttribute也可以处理，当然@RequestBody也能处理）；
    multipart/form-data, 不能处理（即使用@RequestBody不能处理这种格式的数据）；
    其他格式， 必须（其他格式包括application/json, application/xml等。这些格式的数据，必须使用@RequestBody来处理）；
代码:
package com.example.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import com.example.bean.RequestLoginBean;
import com.example.response.BaseResponse;
import com.google.gson.Gson;

@RestController
@RequestMapping(value = "/index")
public class Login {

	/**
	 * index home
	 * 
	 * @return
	 */
	@RequestMapping(value = "/home")
	public String home() {
		return "index home";
	}
	
	/**
	 * 得到1个参数
	 * 
	 * @param name
	 *            用户名
	 * @return 返回结果
	 */
	@GetMapping(value = "/{name}")
	public String index(@PathVariable String name) {
		return "oh you are " + name + "<br> nice to meet you";// \n不起作用了,那就直接用html中的标签吧
	}

	/**
	 * 简单post请求
	 * 
	 * @param name
	 * @param pwd
	 * @return
	 */
	@RequestMapping(value = "/testpost", method = RequestMethod.POST)
	public String testpost() {
		System.out.println("hello  test post");
		return "ok";
	}

	/**
	 * 同时得到两个参数
	 * 
	 * @param name
	 *            用户名
	 * @param pwd
	 *            密码
	 * @return 返回结果
	 */
	@GetMapping(value = "/login/{name}&{pwd}")
	public String login(@PathVariable String name, @PathVariable String pwd) {
		if (name.equals("admin") && pwd.equals("admin")) {
			return "hello welcome admin";
		} else {
			return "oh sorry user name or password is wrong";
		}
	}

	/**
	 * 通过get请求去登陆
	 * 
	 * @param name
	 * @param pwd
	 * @return
	 */
	@RequestMapping(value = "/loginbyget", method = RequestMethod.GET)
	public String loginByGet(@RequestParam(value = "name", required = true) String name,
			@RequestParam(value = "pwd", required = true) String pwd) {
		return login4Return(name, pwd);
	}

	/**
	 * 通过post请求去登陆
	 * 
	 * @param name
	 * @param pwd
	 * @return
	 */
	@RequestMapping(value = "/loginbypost", method = RequestMethod.POST)
	public String loginByPost(@RequestParam(value = "name", required = true) String name,
			@RequestParam(value = "pwd", required = true) String pwd) {
		System.out.println("hello post");
		return login4Return(name, pwd);
	}

	/**
	 * 参数为一个bean对象.spring会自动为我们关联映射
	 * @param loginBean
	 * @return
	 */
	@RequestMapping(value = "/loginbypost1", method = { RequestMethod.POST, RequestMethod.GET })
	public String loginByPost1(RequestLoginBean loginBean) {
		if (null != loginBean) {
			return login4Return(loginBean.getName(), loginBean.getPwd());
		} else {
			return "error";
		}
	}
	
	/**
	 * 请求内容是一个json串,spring会自动把他和我们的参数bean对应起来,不过要加@RequestBody注解
	 * 
	 * @param name
	 * @param pwd
	 * @return
	 */
	@RequestMapping(value = "/loginbypost2", method = { RequestMethod.POST, RequestMethod.GET })
	public String loginByPost2(@RequestBody RequestLoginBean loginBean) {
		if (null != loginBean) {
			return login4Return(loginBean.getName(), loginBean.getPwd());
		} else {
			return "error";
		}
	}

	


	/**
	 * 对登录做出响应处理的方法
	 * 
	 * @param name
	 *            用户名
	 * @param pwd
	 *            密码
	 * @return 返回处理结果
	 */
	private String login4Return(String name, String pwd) {
		String result;
		BaseResponse response = new BaseResponse();
		if (name.equals("admin") && pwd.equals("admin")) {
			result = "hello welcome admin";
			response.setState(true);
		} else {
			result = "oh sorry user name or password is wrong";
			response.setState(false);
		}
		System.out.println("收到请求,请求结果:" + result);
		return new Gson().toJson(response);
	}
}
