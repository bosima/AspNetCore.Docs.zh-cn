---
title: 托管和部署 Blazor 客户端
author: guardrex
description: 了解如何使用 ASP.NET Core、内容分发网络 (CDN)、文件服务器和 GitHub 页来托管和部署 Blazor 应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: host-and-deploy/blazor/client-side
ms.openlocfilehash: 01a612029f415f583908c3bf2adc2e6d35167acb
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614633"
---
# <a name="host-and-deploy-blazor-client-side"></a><span data-ttu-id="7c47e-103">托管和部署 Blazor 客户端</span><span class="sxs-lookup"><span data-stu-id="7c47e-103">Host and deploy Blazor client-side</span></span>

<span data-ttu-id="7c47e-104">作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="7c47e-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

## <a name="host-configuration-values"></a><span data-ttu-id="7c47e-105">主机配置值</span><span class="sxs-lookup"><span data-stu-id="7c47e-105">Host configuration values</span></span>

<span data-ttu-id="7c47e-106">使用[客户端托管模型](xref:blazor/hosting-models#client-side-hosting-model)的 Blazor 应用可以接受以下主机配置值，作为开发环境中运行时的命令行参数。</span><span class="sxs-lookup"><span data-stu-id="7c47e-106">Blazor apps that use the [client-side hosting model](xref:blazor/hosting-models#client-side-hosting-model) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="7c47e-107">内容根</span><span class="sxs-lookup"><span data-stu-id="7c47e-107">Content Root</span></span>

<span data-ttu-id="7c47e-108">`--contentroot` 参数设置包含应用内容文件的目录的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="7c47e-108">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files.</span></span> <span data-ttu-id="7c47e-109">在下面的示例中，`/content-root-path` 是应用的内容根路径。</span><span class="sxs-lookup"><span data-stu-id="7c47e-109">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="7c47e-110">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="7c47e-110">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="7c47e-111">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="7c47e-111">From the app's directory, execute:</span></span>

  ```console
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="7c47e-112">在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目。</span><span class="sxs-lookup"><span data-stu-id="7c47e-112">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="7c47e-113">如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。</span><span class="sxs-lookup"><span data-stu-id="7c47e-113">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="7c47e-114">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数。</span><span class="sxs-lookup"><span data-stu-id="7c47e-114">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="7c47e-115">在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="7c47e-115">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="7c47e-116">基路径</span><span class="sxs-lookup"><span data-stu-id="7c47e-116">Path base</span></span>

<span data-ttu-id="7c47e-117">`--pathbase` 参数可设置使用非根虚拟路径本地运行的应用的应用基路径（将 `<base>` 标记 `href` 针对暂存和生产设置为 `/` 之外的路径）。</span><span class="sxs-lookup"><span data-stu-id="7c47e-117">The `--pathbase` argument sets the app base path for an app run locally with a non-root virtual path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="7c47e-118">在下面的示例中，`/virtual-path` 是应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="7c47e-118">In the following examples, `/virtual-path` is the app's path base.</span></span> <span data-ttu-id="7c47e-119">有关详细信息，请参阅[应用基路径](#app-base-path)部分。</span><span class="sxs-lookup"><span data-stu-id="7c47e-119">For more information, see the [App base path](#app-base-path) section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7c47e-120">不同于向 `href` 标记的 `<base>` 提供的路径，传递 `--pathbase` 参数值时不包括尾部反斜杠 (`/`)。</span><span class="sxs-lookup"><span data-stu-id="7c47e-120">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="7c47e-121">如果在 `<base>` 标记中以 `<base href="/CoolApp/">` 形式（包括尾部反斜杠）提供应用基路径，则以 `--pathbase=/CoolApp` 形式（无尾部反斜杠）传递命令行参数值。</span><span class="sxs-lookup"><span data-stu-id="7c47e-121">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="7c47e-122">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="7c47e-122">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="7c47e-123">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="7c47e-123">From the app's directory, execute:</span></span>

  ```console
  dotnet run --pathbase=/virtual-path
  ```

* <span data-ttu-id="7c47e-124">在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目。</span><span class="sxs-lookup"><span data-stu-id="7c47e-124">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="7c47e-125">如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。</span><span class="sxs-lookup"><span data-stu-id="7c47e-125">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/virtual-path"
  ```

* <span data-ttu-id="7c47e-126">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数。</span><span class="sxs-lookup"><span data-stu-id="7c47e-126">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="7c47e-127">在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="7c47e-127">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/virtual-path
  ```

### <a name="urls"></a><span data-ttu-id="7c47e-128">URL</span><span class="sxs-lookup"><span data-stu-id="7c47e-128">URLs</span></span>

<span data-ttu-id="7c47e-129">`--urls` 参数设置 IP 地址或主机地址，其中包含侦听请求的端口和协议。</span><span class="sxs-lookup"><span data-stu-id="7c47e-129">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="7c47e-130">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="7c47e-130">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="7c47e-131">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="7c47e-131">From the app's directory, execute:</span></span>

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="7c47e-132">在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目。</span><span class="sxs-lookup"><span data-stu-id="7c47e-132">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="7c47e-133">如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。</span><span class="sxs-lookup"><span data-stu-id="7c47e-133">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="7c47e-134">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数。</span><span class="sxs-lookup"><span data-stu-id="7c47e-134">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="7c47e-135">在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="7c47e-135">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deployment"></a><span data-ttu-id="7c47e-136">部署</span><span class="sxs-lookup"><span data-stu-id="7c47e-136">Deployment</span></span>

<span data-ttu-id="7c47e-137">使用[客户端托管模型](xref:blazor/hosting-models#client-side-hosting-model)：</span><span class="sxs-lookup"><span data-stu-id="7c47e-137">With the [client-side hosting model](xref:blazor/hosting-models#client-side-hosting-model):</span></span>

* <span data-ttu-id="7c47e-138">将 Blazor 应用、其依赖项以及 .NET 运行时下载到浏览器。</span><span class="sxs-lookup"><span data-stu-id="7c47e-138">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="7c47e-139">应用将在浏览器线程中直接执行。</span><span class="sxs-lookup"><span data-stu-id="7c47e-139">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="7c47e-140">支持以下策略之一：</span><span class="sxs-lookup"><span data-stu-id="7c47e-140">Either of the following strategies is supported:</span></span>
  * <span data-ttu-id="7c47e-141">Blazor 应用由 ASP.NET Core 应用提供服务。</span><span class="sxs-lookup"><span data-stu-id="7c47e-141">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="7c47e-142">[使用 ASP.NET Core 进行托管部署](#hosted-deployment-with-aspnet-core)部分中介绍了此策略。</span><span class="sxs-lookup"><span data-stu-id="7c47e-142">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
  * <span data-ttu-id="7c47e-143">Blazor 应用位于静态托管 Web 服务器或服务中，其中未使用 .NET 对 Blazor 应用提供服务。</span><span class="sxs-lookup"><span data-stu-id="7c47e-143">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="7c47e-144">[独立部署](#standalone-deployment)部分中介绍了此策略。</span><span class="sxs-lookup"><span data-stu-id="7c47e-144">This strategy is covered in the [Standalone deployment](#standalone-deployment) section.</span></span>

## <a name="configure-the-linker"></a><span data-ttu-id="7c47e-145">配置链接器</span><span class="sxs-lookup"><span data-stu-id="7c47e-145">Configure the Linker</span></span>

<span data-ttu-id="7c47e-146">Blazor 对每个生成执行中间语言 (IL) 链接，以从输出程序集中删除不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="7c47e-146">Blazor performs Intermediate Language (IL) linking on each build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="7c47e-147">可以在生成时控制程序集链接。</span><span class="sxs-lookup"><span data-stu-id="7c47e-147">Assembly linking can be controlled on build.</span></span> <span data-ttu-id="7c47e-148">有关更多信息，请参见<xref:host-and-deploy/blazor/configure-linker>。</span><span class="sxs-lookup"><span data-stu-id="7c47e-148">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="7c47e-149">重写 URL，以实现正确路由</span><span class="sxs-lookup"><span data-stu-id="7c47e-149">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="7c47e-150">客户端应用中对页组件的路由请求不如服务器端对托管应用的路由请求简单。</span><span class="sxs-lookup"><span data-stu-id="7c47e-150">Routing requests for page components in a client-side app isn't as simple as routing requests to a server-side, hosted app.</span></span> <span data-ttu-id="7c47e-151">请考虑具有两个页面的客户端应用：</span><span class="sxs-lookup"><span data-stu-id="7c47e-151">Consider a client-side app with two pages:</span></span>

* <span data-ttu-id="7c47e-152">Main.cshtml &ndash; 在应用的根目录下加载，且包含“关于”页链接 (`href="About"`)。</span><span class="sxs-lookup"><span data-stu-id="7c47e-152">**_Main.cshtml_** &ndash; Loads at the root of the app and contains a link to the About page (`href="About"`).</span></span>
* <span data-ttu-id="7c47e-153">About.cshtml &ndash;“关于”页。</span><span class="sxs-lookup"><span data-stu-id="7c47e-153">**_About.cshtml_** &ndash; About page.</span></span>

<span data-ttu-id="7c47e-154">使用浏览器的地址栏（例如，`https://www.contoso.com/`）请求应用的默认文档：</span><span class="sxs-lookup"><span data-stu-id="7c47e-154">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="7c47e-155">浏览器发出请求。</span><span class="sxs-lookup"><span data-stu-id="7c47e-155">The browser makes a request.</span></span>
1. <span data-ttu-id="7c47e-156">返回默认页，通常为 index.html。</span><span class="sxs-lookup"><span data-stu-id="7c47e-156">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="7c47e-157">index.html 启动应用。</span><span class="sxs-lookup"><span data-stu-id="7c47e-157">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="7c47e-158">此时，Blazor 的路由器加载，并显示 Razor Main 页 (Main.cshtml)。</span><span class="sxs-lookup"><span data-stu-id="7c47e-158">Blazor's router loads, and the Razor Main page (*Main.cshtml*) is displayed.</span></span>

<span data-ttu-id="7c47e-159">在主页上，选择“关于”页面的链接可加载“关于”页面。</span><span class="sxs-lookup"><span data-stu-id="7c47e-159">On the Main page, selecting the link to the About page loads the About page.</span></span> <span data-ttu-id="7c47e-160">选择“关于”页面的链接适用于客户端，因为 Blazor 路由器阻止浏览器对 Internet 发出请求，针对 `About` 转到 `www.contoso.com`，并为“关于”页本身提供服务。</span><span class="sxs-lookup"><span data-stu-id="7c47e-160">Selecting the link to the About page works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the About page itself.</span></span> <span data-ttu-id="7c47e-161">针对客户端应用中的内部页的所有请求，工作原理都相同：这些请求不会触发对 Internet 上的服务器托管资源的基于浏览器的请求。</span><span class="sxs-lookup"><span data-stu-id="7c47e-161">All of the requests for internal pages *within the client-side app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="7c47e-162">路由器将在内部处理请求。</span><span class="sxs-lookup"><span data-stu-id="7c47e-162">The router handles the requests internally.</span></span>

<span data-ttu-id="7c47e-163">如果针对 `www.contoso.com/About` 使用浏览器的地址栏发出请求，则请求会失败。</span><span class="sxs-lookup"><span data-stu-id="7c47e-163">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="7c47e-164">应用的 Internet 主机上不存在此类资源，所以返回的是“404 - 找不到”响应。</span><span class="sxs-lookup"><span data-stu-id="7c47e-164">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="7c47e-165">由于浏览器针对客户端页面请求基于 Internet 的主机，因此 Web 服务器和托管服务必须将对服务器上非物理方式资源的所有请求重写为 index.html 页。</span><span class="sxs-lookup"><span data-stu-id="7c47e-165">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="7c47e-166">如果返回 index.html，应用的客户端路由器将接管工作并使用正确的资源响应。</span><span class="sxs-lookup"><span data-stu-id="7c47e-166">When *index.html* is returned, the app's client-side router takes over and responds with the correct resource.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="7c47e-167">应用基路径</span><span class="sxs-lookup"><span data-stu-id="7c47e-167">App base path</span></span>

<span data-ttu-id="7c47e-168">应用基路径是服务器上的虚拟应用根路径。</span><span class="sxs-lookup"><span data-stu-id="7c47e-168">The *app base path* is the virtual app root path on the server.</span></span> <span data-ttu-id="7c47e-169">例如，位于 `/CoolApp/` 虚拟文件夹中 Contoso 服务器上的应用可通过 `https://www.contoso.com/CoolApp` 进行访问，并且该应用的虚拟基路径为 `/CoolApp/`。</span><span class="sxs-lookup"><span data-stu-id="7c47e-169">For example, an app that resides on the Contoso server in a virtual folder at `/CoolApp/` is reached at `https://www.contoso.com/CoolApp` and has a virtual base path of `/CoolApp/`.</span></span> <span data-ttu-id="7c47e-170">通过将应用基路径设置为 `CoolApp/`，应用就会知道其在服务器上实际所在的位置。</span><span class="sxs-lookup"><span data-stu-id="7c47e-170">By setting the app base path to `CoolApp/`, the app is made aware of where it virtually resides on the server.</span></span> <span data-ttu-id="7c47e-171">应用可使用应用基路径通过不在根目录中的组件构造应用根目录的相对 URL。</span><span class="sxs-lookup"><span data-stu-id="7c47e-171">The app can use the app base path to construct URLs relative to the app root from a component that isn't in the root directory.</span></span> <span data-ttu-id="7c47e-172">通过这种方式，位于目录结构不同级别的组件可生成指向整个应用其他位置的资源链接。</span><span class="sxs-lookup"><span data-stu-id="7c47e-172">This allows components that exist at different levels of the directory structure to build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="7c47e-173">应用基路径也用于截获超链接单击，其中链接的 `href` 目标位于应用基路径 URI 空间中 &mdash; Blazor 路由器可处理内部导航。</span><span class="sxs-lookup"><span data-stu-id="7c47e-173">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="7c47e-174">在许多托管方案中，应用的服务器虚拟路径为应用的根目录。</span><span class="sxs-lookup"><span data-stu-id="7c47e-174">In many hosting scenarios, the server's virtual path to the app is the root of the app.</span></span> <span data-ttu-id="7c47e-175">在这些情况下，应用基路径为正斜杠 (`<base href="/" />`)，它是应用的默认配置。</span><span class="sxs-lookup"><span data-stu-id="7c47e-175">In these cases, the app base path is a forward slash (`<base href="/" />`), which is the default configuration for an app.</span></span> <span data-ttu-id="7c47e-176">在其他托管方案中，例如 GitHub 页和 IIS 虚拟目录或子应用程序，应用基路径必须设置为应用的服务器虚拟路径。</span><span class="sxs-lookup"><span data-stu-id="7c47e-176">In other hosting scenarios, such as GitHub Pages and IIS virtual directories or sub-applications, the app base path must be set to the server's virtual path to the app.</span></span> <span data-ttu-id="7c47e-177">要设置应用的基路径，请在 `<head>` 标记元素中找到的 index.html 中添加或更新 `<base>` 标记。</span><span class="sxs-lookup"><span data-stu-id="7c47e-177">To set the app's base path, add or update the `<base>` tag in *index.html* found within the `<head>` tag elements.</span></span> <span data-ttu-id="7c47e-178">将 `href` 属性值设置为 `virtual-path/`（需要尾部反斜杠），其中 `virtual-path/` 是应用的服务器上的完整虚拟应用根路径。</span><span class="sxs-lookup"><span data-stu-id="7c47e-178">Set the `href` attribute value to `virtual-path/` (the trailing slash is required), where `virtual-path/` is the full virtual app root path on the server for the app.</span></span> <span data-ttu-id="7c47e-179">在上述示例中，虚拟路径设置为 `CoolApp/`：`<base href="CoolApp/">`。</span><span class="sxs-lookup"><span data-stu-id="7c47e-179">In the preceding example, the virtual path is set to `CoolApp/`: `<base href="CoolApp/">`.</span></span>

<span data-ttu-id="7c47e-180">对于配置了非根虚拟路径的应用（例如，`<base href="CoolApp/">`），当在本地运行应用时，将无法查找其资源。</span><span class="sxs-lookup"><span data-stu-id="7c47e-180">For an app with a non-root virtual path configured (for example, `<base href="CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="7c47e-181">要在本地开发和测试过程中解决此问题，可提供 path base 参数，用于匹配运行时 `<base>` 标记的 `href` 值。</span><span class="sxs-lookup"><span data-stu-id="7c47e-181">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span>

<span data-ttu-id="7c47e-182">要在本地运行应用时传递带根路径 (`/`) 的 path base 参数，请在应用的目录中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="7c47e-182">To pass the path base argument with the root path (`/`) when running the app locally, execute the following command from the app's directory:</span></span>

```console
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="7c47e-183">应用在 `http://localhost:port/CoolApp` 本地响应。</span><span class="sxs-lookup"><span data-stu-id="7c47e-183">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="7c47e-184">有关详细信息，请参阅[基路径主机配置值](#path-base)部分。</span><span class="sxs-lookup"><span data-stu-id="7c47e-184">For more information, see the section on the [path base host configuration value](#path-base).</span></span>

<span data-ttu-id="7c47e-185">如果应用使用[客户端托管模型](xref:blazor/hosting-models#client-side-hosting-model)（基于 Blazor 项目模板），并且作为 ASP.NET Core 应用中的 IIS 子应用程序进行托管，则有必要禁用已继承的 ASP.NET Core 模块处理程序或确保根应用的 web.config 文件中的 `<handlers>` 部分未由子应用继承。</span><span class="sxs-lookup"><span data-stu-id="7c47e-185">If an app uses the [client-side hosting model](xref:blazor/hosting-models#client-side-hosting-model) (based on the **Blazor** project template) and is hosted as an IIS sub-application in an ASP.NET Core app, it's important to disable the inherited ASP.NET Core Module handler or make sure the root (parent) app's `<handlers>` section in the *web.config* file isn't inherited by the sub-app.</span></span>

<span data-ttu-id="7c47e-186">通过向文件添加 `<handlers>` 部分，删除应用已发布 web.config 文件中的处理程序：</span><span class="sxs-lookup"><span data-stu-id="7c47e-186">Remove the handler in the app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

```xml
<handlers>
  <remove name="aspNetCore" />
</handlers>
```

<span data-ttu-id="7c47e-187">或者，通过使用 `<location>` 元素并且将 `inheritInChildApplications` 设置为 `false`，禁止继承根（父级）应用的 `<system.webServer>` 部分：</span><span class="sxs-lookup"><span data-stu-id="7c47e-187">Alternatively, disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" ... />
      </handlers>
      <aspNetCore ... />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="7c47e-188">如本部分中所述，除配置应用的基路径外，还需删除处理程序或禁用继承。</span><span class="sxs-lookup"><span data-stu-id="7c47e-188">Removing the handler or disabling inheritance is performed in addition to configuring the app's base path as described in this section.</span></span> <span data-ttu-id="7c47e-189">在 IIS 中配置子应用时，在应用的 index.html 文件中将应用基路径设置为 IIS 别名。</span><span class="sxs-lookup"><span data-stu-id="7c47e-189">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="7c47e-190">使用 ASP.NET Core 进行托管部署</span><span class="sxs-lookup"><span data-stu-id="7c47e-190">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="7c47e-191">托管部署通过在服务器上运行的 [ASP.NET Core 应用](xref:index)为浏览器提供客户端 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="7c47e-191">A *hosted deployment* serves the client-side Blazor app to browsers from an [ASP.NET Core app](xref:index) that runs on a server.</span></span>

<span data-ttu-id="7c47e-192">Blazor 应用随附于已发布输出中的 ASP.NET Core 应用，因此这两个应用将一起进行部署。</span><span class="sxs-lookup"><span data-stu-id="7c47e-192">The Blazor app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="7c47e-193">需要能够托管 ASP.NET Core 应用的 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="7c47e-193">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="7c47e-194">对于托管部署，Visual Studio 包括 Blazor（托管 ASP.NET Core）项目模板（使用 [dotnet new](/dotnet/core/tools/dotnet-new) 命令时为 `blazorhosted` 模板）。</span><span class="sxs-lookup"><span data-stu-id="7c47e-194">For a hosted deployment, Visual Studio includes the **Blazor (ASP.NET Core hosted)** project template (`blazorhosted` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<span data-ttu-id="7c47e-195">有关托管和部署 ASP.NET Core 应用的详细信息，请参阅 <xref:host-and-deploy/index>。</span><span class="sxs-lookup"><span data-stu-id="7c47e-195">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="7c47e-196">若要了解如何部署到 Azure 应用服务，请参阅 <xref:tutorials/publish-to-azure-webapp-using-vs>。</span><span class="sxs-lookup"><span data-stu-id="7c47e-196">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="7c47e-197">独立部署</span><span class="sxs-lookup"><span data-stu-id="7c47e-197">Standalone deployment</span></span>

<span data-ttu-id="7c47e-198">独立部署将客户端 Blazor 应用作为客户端直接请求的一组静态文件。</span><span class="sxs-lookup"><span data-stu-id="7c47e-198">A *standalone deployment* serves the client-side Blazor app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="7c47e-199">Web 服务器不用于对 Blazor 提供服务。</span><span class="sxs-lookup"><span data-stu-id="7c47e-199">A web server isn't used to serve the Blazor app.</span></span>

### <a name="iis"></a><span data-ttu-id="7c47e-200">IIS</span><span class="sxs-lookup"><span data-stu-id="7c47e-200">IIS</span></span>

<span data-ttu-id="7c47e-201">IIS 是适用于 Blazor 应用的强大静态文件服务器。</span><span class="sxs-lookup"><span data-stu-id="7c47e-201">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="7c47e-202">要配置 IIS 以托管 Blazor，请参阅[在 IIS 上生成静态网站](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="7c47e-202">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="7c47e-203">已发布的资产是在 /bin/Release/{TARGET FRAMEWORK}/publish 文件夹中创建。</span><span class="sxs-lookup"><span data-stu-id="7c47e-203">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="7c47e-204">在 Web 服务器或托管服务上托管“publish”文件夹内容。</span><span class="sxs-lookup"><span data-stu-id="7c47e-204">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="7c47e-205">web.config</span><span class="sxs-lookup"><span data-stu-id="7c47e-205">web.config</span></span>

<span data-ttu-id="7c47e-206">发布 Blazor 项目时，将使用以下 IIS 配置创建 web.config 文件：</span><span class="sxs-lookup"><span data-stu-id="7c47e-206">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="7c47e-207">对以下文件扩展名设置 MIME 类型：</span><span class="sxs-lookup"><span data-stu-id="7c47e-207">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="7c47e-208">.dll &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="7c47e-208">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="7c47e-209">.json &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="7c47e-209">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="7c47e-210">.wasm &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="7c47e-210">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="7c47e-211">.woff &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="7c47e-211">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="7c47e-212">.woff2 &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="7c47e-212">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="7c47e-213">对以下 MIME 类型启用 HTTP 压缩：</span><span class="sxs-lookup"><span data-stu-id="7c47e-213">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="7c47e-214">建立 URL 重写模块规则：</span><span class="sxs-lookup"><span data-stu-id="7c47e-214">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="7c47e-215">提供应用的静态资产驻留所在的子目录 ({ASSEMBLY NAME}/dist/{PATH REQUESTED})。</span><span class="sxs-lookup"><span data-stu-id="7c47e-215">Serve the sub-directory where the app's static assets reside (*{ASSEMBLY NAME}/dist/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="7c47e-216">创建 SPA 回退路由，以便非文件资产请求能够重定向到应用的静态资产文件夹中的默认文档 ({ASSEMBLY NAME}/dist/index.html)。</span><span class="sxs-lookup"><span data-stu-id="7c47e-216">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*{ASSEMBLY NAME}/dist/index.html*).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="7c47e-217">安装 URL 重写模块</span><span class="sxs-lookup"><span data-stu-id="7c47e-217">Install the URL Rewrite Module</span></span>

<span data-ttu-id="7c47e-218">重写 URL 必须使用 [URL 重写模块](https://www.iis.net/downloads/microsoft/url-rewrite)。</span><span class="sxs-lookup"><span data-stu-id="7c47e-218">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="7c47e-219">此模块默认不安装，且不适用于安装为 Web 服务器 (IIS) 角色服务功能。</span><span class="sxs-lookup"><span data-stu-id="7c47e-219">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="7c47e-220">必须从 IIS 网站下载该模块。</span><span class="sxs-lookup"><span data-stu-id="7c47e-220">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="7c47e-221">使用 Web 平台安装程序安装模块：</span><span class="sxs-lookup"><span data-stu-id="7c47e-221">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="7c47e-222">以本地方式导航到 [URL 重写模块下载页](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)。</span><span class="sxs-lookup"><span data-stu-id="7c47e-222">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="7c47e-223">对于英语版本，请选择“WebPI”以下载 WebPI 安装程序。</span><span class="sxs-lookup"><span data-stu-id="7c47e-223">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="7c47e-224">对于其他语言，请选择适当的服务器体系结构 (x86/x64) 下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="7c47e-224">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="7c47e-225">将安装程序复制到服务器。</span><span class="sxs-lookup"><span data-stu-id="7c47e-225">Copy the installer to the server.</span></span> <span data-ttu-id="7c47e-226">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="7c47e-226">Run the installer.</span></span> <span data-ttu-id="7c47e-227">选择“安装”按钮，并接受许可条款。</span><span class="sxs-lookup"><span data-stu-id="7c47e-227">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="7c47e-228">安装完成后无需重启服务器。</span><span class="sxs-lookup"><span data-stu-id="7c47e-228">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="7c47e-229">配置网站</span><span class="sxs-lookup"><span data-stu-id="7c47e-229">Configure the website</span></span>

<span data-ttu-id="7c47e-230">将网站的物理路径设置为应用的文件夹。</span><span class="sxs-lookup"><span data-stu-id="7c47e-230">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="7c47e-231">该文件夹包含：</span><span class="sxs-lookup"><span data-stu-id="7c47e-231">The folder contains:</span></span>

* <span data-ttu-id="7c47e-232">web.config 文件，IIS 使用该文件配置网站，包括所需的重定向规则和文件内容类型。</span><span class="sxs-lookup"><span data-stu-id="7c47e-232">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="7c47e-233">应用的静态资产文件夹。</span><span class="sxs-lookup"><span data-stu-id="7c47e-233">The app's static asset folder.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="7c47e-234">疑难解答</span><span class="sxs-lookup"><span data-stu-id="7c47e-234">Troubleshooting</span></span>

<span data-ttu-id="7c47e-235">如果你看到“500 - 内部服务器错误”，且 IIS 管理器在尝试访问网站配置时抛出错误，请确认是否已安装 URL 重写模块。</span><span class="sxs-lookup"><span data-stu-id="7c47e-235">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="7c47e-236">如果未安装该模块，则 IIS 无法分析 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="7c47e-236">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="7c47e-237">这可以防止 IIS 管理器加载网站配置，并防止网站对 Blazor 的静态文件提供服务。</span><span class="sxs-lookup"><span data-stu-id="7c47e-237">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="7c47e-238">有关排查部署到 IIS 的问题的详细信息，请参阅 <xref:host-and-deploy/iis/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="7c47e-238">For more information on troubleshooting deployments to IIS, see <xref:host-and-deploy/iis/troubleshoot>.</span></span>

### <a name="nginx"></a><span data-ttu-id="7c47e-239">Nginx</span><span class="sxs-lookup"><span data-stu-id="7c47e-239">Nginx</span></span>

<span data-ttu-id="7c47e-240">以下 nginx.conf 文件已简化，以展示如何将 Nginx 配置为，每当在磁盘上找不到相应文件时，发送 index.html 文件。</span><span class="sxs-lookup"><span data-stu-id="7c47e-240">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="7c47e-241">有关生产 Nginx Web 服务器配置的详细信息，请参阅 [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)（创建 NGINX 增强版和 NGINX 配置文件）。</span><span class="sxs-lookup"><span data-stu-id="7c47e-241">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="7c47e-242">Docker 中的 Nginx</span><span class="sxs-lookup"><span data-stu-id="7c47e-242">Nginx in Docker</span></span>

<span data-ttu-id="7c47e-243">要使用 Nginx 在 Docker 中托管 Blazor，请设置 Dockerfile，以使用基于 Alpine 的 Nginx 映像。</span><span class="sxs-lookup"><span data-stu-id="7c47e-243">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="7c47e-244">更新 Dockerfile，以将 nginx.config 文件复制到容器。</span><span class="sxs-lookup"><span data-stu-id="7c47e-244">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="7c47e-245">向 Dockerfile 添加一行，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="7c47e-245">Add one line to the Dockerfile, as shown in the following example:</span></span>

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="github-pages"></a><span data-ttu-id="7c47e-246">GitHub 页</span><span class="sxs-lookup"><span data-stu-id="7c47e-246">GitHub Pages</span></span>

<span data-ttu-id="7c47e-247">要处理 URL 重写，请使用脚本添加 404.html 文件，该脚本可处理到 index.html 页的重定向请求。</span><span class="sxs-lookup"><span data-stu-id="7c47e-247">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="7c47e-248">关于社区提供的示例实现，请参阅[用于 GitHub 页的单页应用](http://spa-github-pages.rafrex.com/)（[GitHub 上的 rafrex/spa-github-pages](https://github.com/rafrex/spa-github-pages#readme)）。</span><span class="sxs-lookup"><span data-stu-id="7c47e-248">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](http://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="7c47e-249">可在 [GitHub 上的 blazor-demo/blazor-demo.github.io](https://github.com/blazor-demo/blazor-demo.github.io)（[实时站点](https://blazor-demo.github.io/)）查看使用社区方法的示例。</span><span class="sxs-lookup"><span data-stu-id="7c47e-249">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="7c47e-250">如果使用项目站点而非组织站点，请在 index.html 中添加或更新 `<base>` 标记。</span><span class="sxs-lookup"><span data-stu-id="7c47e-250">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="7c47e-251">将 `href` 属性值设置为，包含尾部斜杠的 GitHub 存储库名称（例如，`my-repository/`）。</span><span class="sxs-lookup"><span data-stu-id="7c47e-251">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>