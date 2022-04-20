<h1 align = "center">bug与问题</h1>
1 Springboot 全局响应类与 swagger 冲突
解决方案：1、ControllerAdvice 限定扫描类
        2、实现 ResponseBodyAdvice，重写 supports()
            return !returnType.getDeclaringClass().equals(Docket.class);
        3、实现 ResponseBodyAdvice，重写 beforeBodyWrite()
            // 这里需要过滤掉swagger的相关返回
            if (body instanceof ApiResult || body instanceof Json || body instanceof UiConfiguration || (body instanceof ArrayList && ((ArrayList) body).get(0) instanceof SwaggerResource))
                return body;
            return new ApiResult(body);

2