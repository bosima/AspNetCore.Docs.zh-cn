---
title: 为 ASP.NET Core 强制实施内容安全策略 Blazor
author: guardrex
description: 了解如何将内容安全策略（CSP）与 ASP.NET Core Blazor 应用程序结合使用，以帮助防范跨站点脚本（XSS）攻击。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/content-security-policy
ms.openlocfilehash: 1cfebf7b3d3bbb98a671b6f2db7c6518cda74b65
ms.sourcegitcommit: 51c86c003ab5436598dbc42f26ea4a83a795fd6e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "78964534"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-opno-locblazor"></a><span data-ttu-id="b8f98-103">为 ASP.NET Core 强制实施内容安全策略 Blazor</span><span class="sxs-lookup"><span data-stu-id="b8f98-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="b8f98-104">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b8f98-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="b8f98-105">[跨站点脚本（XSS）](xref:security/cross-site-scripting)是一个安全漏洞，攻击者将一个或多个恶意客户端脚本放入应用程序的呈现内容中。</span><span class="sxs-lookup"><span data-stu-id="b8f98-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="b8f98-106">内容安全策略（CSP）通过通知浏览器的有效情况来帮助防范 XSS 攻击：</span><span class="sxs-lookup"><span data-stu-id="b8f98-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="b8f98-107">已加载内容的源，包括脚本、样式表和图像。</span><span class="sxs-lookup"><span data-stu-id="b8f98-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="b8f98-108">页执行的操作，指定窗体的允许的 URL 目标。</span><span class="sxs-lookup"><span data-stu-id="b8f98-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="b8f98-109">可以加载的插件。</span><span class="sxs-lookup"><span data-stu-id="b8f98-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="b8f98-110">若要将 CSP 应用到应用，开发人员需要在一个或多个 `Content-Security-Policy` 标头或 `<meta>` 标记中指定若干 CSP 内容安全*指令*。</span><span class="sxs-lookup"><span data-stu-id="b8f98-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="b8f98-111">加载页面时，浏览器会对策略进行评估。</span><span class="sxs-lookup"><span data-stu-id="b8f98-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="b8f98-112">浏览器将检查页的源并确定它们是否满足内容安全指令的要求。</span><span class="sxs-lookup"><span data-stu-id="b8f98-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="b8f98-113">如果资源不满足策略指令，浏览器不会加载资源。</span><span class="sxs-lookup"><span data-stu-id="b8f98-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="b8f98-114">例如，请考虑不允许第三方脚本的策略。</span><span class="sxs-lookup"><span data-stu-id="b8f98-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="b8f98-115">当某个页面包含 `src` 属性中具有第三方来源的 `<script>` 标记时，浏览器将阻止加载该脚本。</span><span class="sxs-lookup"><span data-stu-id="b8f98-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="b8f98-116">大多数新式桌面和移动浏览器（包括 Chrome、Edge、Firefox、Opera 和 Safari）都支持 CSP。</span><span class="sxs-lookup"><span data-stu-id="b8f98-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="b8f98-117">建议将 CSP 用于 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="b8f98-117">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="b8f98-118">策略指令</span><span class="sxs-lookup"><span data-stu-id="b8f98-118">Policy directives</span></span>

<span data-ttu-id="b8f98-119">最少指定 Blazor 应用的以下指令和源。</span><span class="sxs-lookup"><span data-stu-id="b8f98-119">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="b8f98-120">根据需要添加其他指令和源。</span><span class="sxs-lookup"><span data-stu-id="b8f98-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="b8f98-121">以下指令用于本文的[应用策略](#apply-the-policy)部分，其中提供了 Blazor WebAssembly 和 Blazor 服务器的示例安全策略：</span><span class="sxs-lookup"><span data-stu-id="b8f98-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="b8f98-122">[基础 uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; 限制页的 `<base>` 标记的 url。</span><span class="sxs-lookup"><span data-stu-id="b8f98-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="b8f98-123">指定 `self`，以指示应用程序的源（包括方案和端口号）是有效的源。</span><span class="sxs-lookup"><span data-stu-id="b8f98-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="b8f98-124">[阻止所有混合内容](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content)&ndash; 阻止加载混合 HTTP 和 HTTPS 内容。</span><span class="sxs-lookup"><span data-stu-id="b8f98-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="b8f98-125">[默认-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; 指示策略未显式指定的源指令的回退。</span><span class="sxs-lookup"><span data-stu-id="b8f98-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="b8f98-126">指定 `self`，以指示应用程序的源（包括方案和端口号）是有效的源。</span><span class="sxs-lookup"><span data-stu-id="b8f98-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="b8f98-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; 指示映像的有效源。</span><span class="sxs-lookup"><span data-stu-id="b8f98-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; Indicates valid sources for images.</span></span>
  * <span data-ttu-id="b8f98-128">指定 `data:` 允许从 `data:` Url 加载图像。</span><span class="sxs-lookup"><span data-stu-id="b8f98-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="b8f98-129">指定 `https:` 允许从 HTTPS 终结点加载图像。</span><span class="sxs-lookup"><span data-stu-id="b8f98-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="b8f98-130">[对象-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; 指示 `<object>`、`<embed>`和 `<applet>` 标记的有效源。</span><span class="sxs-lookup"><span data-stu-id="b8f98-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="b8f98-131">指定 `none` 以阻止所有 URL 源。</span><span class="sxs-lookup"><span data-stu-id="b8f98-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="b8f98-132">[脚本-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; 表示脚本的有效源。</span><span class="sxs-lookup"><span data-stu-id="b8f98-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="b8f98-133">指定用于启动脚本的 `https://stackpath.bootstrapcdn.com/` 主机源。</span><span class="sxs-lookup"><span data-stu-id="b8f98-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="b8f98-134">指定 `self`，以指示应用程序的源（包括方案和端口号）是有效的源。</span><span class="sxs-lookup"><span data-stu-id="b8f98-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="b8f98-135">在 Blazor WebAssembly 应用中：</span><span class="sxs-lookup"><span data-stu-id="b8f98-135">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="b8f98-136">指定下列哈希，以允许所需的 Blazor WebAssembly 内联脚本加载：</span><span class="sxs-lookup"><span data-stu-id="b8f98-136">Specify the following hashes to permit the required Blazor WebAssembly inline scripts to load:</span></span>
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * <span data-ttu-id="b8f98-137">指定 `unsafe-eval`，以使用从字符串创建代码 `eval()` 和方法。</span><span class="sxs-lookup"><span data-stu-id="b8f98-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="b8f98-138">在 Blazor Server 应用程序中，为内联脚本指定为样式表执行回退检测的 `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` 哈希。</span><span class="sxs-lookup"><span data-stu-id="b8f98-138">In a Blazor Server app, specify the `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash for the inline script that performs fallback detection for stylesheets.</span></span>
* <span data-ttu-id="b8f98-139">[样式-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; 指示样式表的有效源。</span><span class="sxs-lookup"><span data-stu-id="b8f98-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="b8f98-140">为启动样式表指定 `https://stackpath.bootstrapcdn.com/` 主机源。</span><span class="sxs-lookup"><span data-stu-id="b8f98-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="b8f98-141">指定 `self`，以指示应用程序的源（包括方案和端口号）是有效的源。</span><span class="sxs-lookup"><span data-stu-id="b8f98-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="b8f98-142">指定 `unsafe-inline` 允许使用内联样式。</span><span class="sxs-lookup"><span data-stu-id="b8f98-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="b8f98-143">Blazor Server apps 中的 UI 需要内联声明，以便在初始请求后重新连接客户端和服务器。</span><span class="sxs-lookup"><span data-stu-id="b8f98-143">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="b8f98-144">在将来的版本中，可能会删除内联样式，以便不再需要 `unsafe-inline`。</span><span class="sxs-lookup"><span data-stu-id="b8f98-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="b8f98-145">[升级-不安全-请求](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests)&ndash; 表明应通过 HTTPS 安全地获取来自不安全（HTTP）源的内容 url。</span><span class="sxs-lookup"><span data-stu-id="b8f98-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash; Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="b8f98-146">除 Microsoft Internet Explorer 外，所有浏览器都支持前面的指令。</span><span class="sxs-lookup"><span data-stu-id="b8f98-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="b8f98-147">获取更多内联脚本的 SHA 哈希：</span><span class="sxs-lookup"><span data-stu-id="b8f98-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="b8f98-148">应用 "[应用策略](#apply-the-policy)" 部分中所示的 CSP。</span><span class="sxs-lookup"><span data-stu-id="b8f98-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="b8f98-149">在本地运行应用程序时，访问浏览器的 "开发人员工具" 控制台。</span><span class="sxs-lookup"><span data-stu-id="b8f98-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="b8f98-150">当存在 CSP 标头或 `meta` 标记时，浏览器将为被阻止的脚本计算并显示哈希。</span><span class="sxs-lookup"><span data-stu-id="b8f98-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="b8f98-151">将浏览器提供的哈希复制到 `script-src` 源。</span><span class="sxs-lookup"><span data-stu-id="b8f98-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="b8f98-152">在每个哈希两边使用单引号。</span><span class="sxs-lookup"><span data-stu-id="b8f98-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="b8f98-153">有关内容安全策略级别2浏览器支持矩阵，请参阅[可以使用：内容安全策略级别 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)。</span><span class="sxs-lookup"><span data-stu-id="b8f98-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="b8f98-154">应用策略</span><span class="sxs-lookup"><span data-stu-id="b8f98-154">Apply the policy</span></span>

<span data-ttu-id="b8f98-155">使用 `<meta>` 标记来应用策略：</span><span class="sxs-lookup"><span data-stu-id="b8f98-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="b8f98-156">将 `http-equiv` 属性的值设置为 `Content-Security-Policy`。</span><span class="sxs-lookup"><span data-stu-id="b8f98-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="b8f98-157">将指令放在 `content` 特性值中。</span><span class="sxs-lookup"><span data-stu-id="b8f98-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="b8f98-158">用分号分隔指令（`;`）。</span><span class="sxs-lookup"><span data-stu-id="b8f98-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="b8f98-159">始终将 `meta` 标记放在 `<head>` 内容中。</span><span class="sxs-lookup"><span data-stu-id="b8f98-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="b8f98-160">以下部分介绍 Blazor WebAssembly 和 Blazor 服务器的示例策略。</span><span class="sxs-lookup"><span data-stu-id="b8f98-160">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="b8f98-161">对于每个版本的 Blazor，本文都对这些示例进行了版本控制。</span><span class="sxs-lookup"><span data-stu-id="b8f98-161">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="b8f98-162">若要使用适合你版本的版本，请在此网页上选择包含**版本**下拉选择器的文档版本。</span><span class="sxs-lookup"><span data-stu-id="b8f98-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="b8f98-163"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b8f98-163"> WebAssembly</span></span>

<span data-ttu-id="b8f98-164">在*wwwroot/index.html*主机页的 `<head>` 内容中，应用[策略指令](#policy-directives)部分中描述的指令：</span><span class="sxs-lookup"><span data-stu-id="b8f98-164">In the `<head>` content of the *wwwroot/index.html* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

### <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="b8f98-165"> 服务器</span><span class="sxs-lookup"><span data-stu-id="b8f98-165"> Server</span></span>

<span data-ttu-id="b8f98-166">在 "*页面/_Host*的 `<head>` 内容" 主机页上，应用[策略指令](#policy-directives)部分中描述的指令：</span><span class="sxs-lookup"><span data-stu-id="b8f98-166">In the `<head>` content of the *Pages/_Host.cshtml* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

## <a name="meta-tag-limitations"></a><span data-ttu-id="b8f98-167">元标记限制</span><span class="sxs-lookup"><span data-stu-id="b8f98-167">Meta tag limitations</span></span>

<span data-ttu-id="b8f98-168">`<meta>` 标记策略不支持以下指令：</span><span class="sxs-lookup"><span data-stu-id="b8f98-168">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="b8f98-169">框架-上级</span><span class="sxs-lookup"><span data-stu-id="b8f98-169">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="b8f98-170">报表到</span><span class="sxs-lookup"><span data-stu-id="b8f98-170">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="b8f98-171">报表-uri</span><span class="sxs-lookup"><span data-stu-id="b8f98-171">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="b8f98-172">处理</span><span class="sxs-lookup"><span data-stu-id="b8f98-172">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="b8f98-173">若要支持前面的指令，请使用名为 `Content-Security-Policy`的标头。</span><span class="sxs-lookup"><span data-stu-id="b8f98-173">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="b8f98-174">指令字符串是标头的值。</span><span class="sxs-lookup"><span data-stu-id="b8f98-174">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="b8f98-175">测试策略并接收冲突报告</span><span class="sxs-lookup"><span data-stu-id="b8f98-175">Test a policy and receive violation reports</span></span>

<span data-ttu-id="b8f98-176">测试有助于确认在生成初始策略时不会无意中阻止第三方脚本。</span><span class="sxs-lookup"><span data-stu-id="b8f98-176">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="b8f98-177">若要在不强制实施策略指令的情况下测试策略，请将基于标头的策略 `<meta>` 标记的 `http-equiv` 属性或标头名称设置为 "`Content-Security-Policy-Report-Only`"。</span><span class="sxs-lookup"><span data-stu-id="b8f98-177">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="b8f98-178">故障报告作为 JSON 文档发送到指定的 URL。</span><span class="sxs-lookup"><span data-stu-id="b8f98-178">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="b8f98-179">有关详细信息，请参阅[MDN web 文档：仅限内容安全策略](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)。</span><span class="sxs-lookup"><span data-stu-id="b8f98-179">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="b8f98-180">若要在策略处于活动状态时报告冲突，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="b8f98-180">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="b8f98-181">报表到</span><span class="sxs-lookup"><span data-stu-id="b8f98-181">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="b8f98-182">报表-uri</span><span class="sxs-lookup"><span data-stu-id="b8f98-182">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="b8f98-183">尽管不再建议使用 `report-uri`，但在所有主要浏览器都支持 `report-to` 之前，都应使用这两个指令。</span><span class="sxs-lookup"><span data-stu-id="b8f98-183">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="b8f98-184">不要专门使用 `report-uri`，因为对 `report-uri` 的支持可能会随时从浏览器*中删除。*</span><span class="sxs-lookup"><span data-stu-id="b8f98-184">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="b8f98-185">完全支持 `report-to` 时，删除对策略中 `report-uri` 的支持。</span><span class="sxs-lookup"><span data-stu-id="b8f98-185">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="b8f98-186">若要跟踪 `report-to`的采用，请参阅[可以使用：报告到](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)。</span><span class="sxs-lookup"><span data-stu-id="b8f98-186">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="b8f98-187">每次发布时测试和更新应用的策略。</span><span class="sxs-lookup"><span data-stu-id="b8f98-187">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b8f98-188">疑难解答</span><span class="sxs-lookup"><span data-stu-id="b8f98-188">Troubleshoot</span></span>

* <span data-ttu-id="b8f98-189">浏览器的 "开发人员工具" 控制台中会出现错误。</span><span class="sxs-lookup"><span data-stu-id="b8f98-189">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="b8f98-190">浏览器提供以下信息：</span><span class="sxs-lookup"><span data-stu-id="b8f98-190">Browsers provide information about:</span></span>
  * <span data-ttu-id="b8f98-191">不符合策略的元素。</span><span class="sxs-lookup"><span data-stu-id="b8f98-191">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="b8f98-192">如何修改策略以允许阻止的项目。</span><span class="sxs-lookup"><span data-stu-id="b8f98-192">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="b8f98-193">仅当客户端的浏览器支持所有包含的指令时，策略才会完全有效。</span><span class="sxs-lookup"><span data-stu-id="b8f98-193">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="b8f98-194">有关当前的浏览器支持矩阵，请参阅[可以使用：内容安全策略](https://caniuse.com/#search=Content-Security-Policy)。</span><span class="sxs-lookup"><span data-stu-id="b8f98-194">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b8f98-195">其他资源</span><span class="sxs-lookup"><span data-stu-id="b8f98-195">Additional resources</span></span>

* [<span data-ttu-id="b8f98-196">MDN web 文档：内容安全策略</span><span class="sxs-lookup"><span data-stu-id="b8f98-196">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="b8f98-197">内容安全策略级别2</span><span class="sxs-lookup"><span data-stu-id="b8f98-197">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="b8f98-198">Google CSP 计算器</span><span class="sxs-lookup"><span data-stu-id="b8f98-198">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)