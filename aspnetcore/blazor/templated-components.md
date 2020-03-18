---
title: ASP.NET Core Blazor 模板化组件
author: guardrex
description: 了解模板化组件如何接受一个或多个 UI 模板作为参数，然后将其用作组件呈现逻辑的一部分。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: b64d6a731e540b13c50b2c6108f75efd0ac9290c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646530"
---
# <a name="aspnet-core-opno-locblazor-templated-components"></a><span data-ttu-id="1ea45-103">ASP.NET Core Blazor 模板化组件</span><span class="sxs-lookup"><span data-stu-id="1ea45-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="1ea45-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="1ea45-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="1ea45-105">模板化组件是接受一个或多个 UI 模板作为参数的组件，然后可将其用作组件呈现逻辑的一部分。</span><span class="sxs-lookup"><span data-stu-id="1ea45-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="1ea45-106">通过模板化组件，可以创作适用面更广、比常规组件更便于重复使用的组件。</span><span class="sxs-lookup"><span data-stu-id="1ea45-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="1ea45-107">下面是一些示例：</span><span class="sxs-lookup"><span data-stu-id="1ea45-107">A couple of examples include:</span></span>

* <span data-ttu-id="1ea45-108">表组件，用户可通过它指定表的标题、行和页脚的模板。</span><span class="sxs-lookup"><span data-stu-id="1ea45-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="1ea45-109">列表组件，用户可通过它指定用于呈现列表中项的模板。</span><span class="sxs-lookup"><span data-stu-id="1ea45-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="1ea45-110">模板参数</span><span class="sxs-lookup"><span data-stu-id="1ea45-110">Template parameters</span></span>

<span data-ttu-id="1ea45-111">通过指定一个或多个 `RenderFragment` 或 `RenderFragment<T>` 类型的组件参数来定义模板化组件。</span><span class="sxs-lookup"><span data-stu-id="1ea45-111">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="1ea45-112">呈现片段，表示要呈现的 UI 段。</span><span class="sxs-lookup"><span data-stu-id="1ea45-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="1ea45-113">`RenderFragment<T>` 采用可在调用呈现片段时指定的类型参数。</span><span class="sxs-lookup"><span data-stu-id="1ea45-113">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="1ea45-114">`TableTemplate` 组件：</span><span class="sxs-lookup"><span data-stu-id="1ea45-114">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="1ea45-115">使用模板化组件时，可以使用与参数名称匹配的子元素（在以下示例中为 `TableHeader` 和 `RowTemplate`）指定模板参数：</span><span class="sxs-lookup"><span data-stu-id="1ea45-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

## <a name="template-context-parameters"></a><span data-ttu-id="1ea45-116">模板上下文参数</span><span class="sxs-lookup"><span data-stu-id="1ea45-116">Template context parameters</span></span>

<span data-ttu-id="1ea45-117">作为元素传递的 `RenderFragment<T>` 类型的组件实参具有一个名为 `context` 的隐式形参（例如前面的代码示例 `@context.PetId`），但可以使用子元素上的 `Context` 属性来更改形参名称。</span><span class="sxs-lookup"><span data-stu-id="1ea45-117">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="1ea45-118">在下面的示例中，`RowTemplate` 元素的 `Context` 属性指定了 `pet` 参数：</span><span class="sxs-lookup"><span data-stu-id="1ea45-118">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="1ea45-119">或者，可以在组件元素上指定 `Context` 属性。</span><span class="sxs-lookup"><span data-stu-id="1ea45-119">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="1ea45-120">指定的 `Context` 属性适用于所有指定的模板参数。</span><span class="sxs-lookup"><span data-stu-id="1ea45-120">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="1ea45-121">如果你想为隐式子内容指定内容参数名称（不包含任何包装子元素），这可能很有用。</span><span class="sxs-lookup"><span data-stu-id="1ea45-121">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="1ea45-122">在下面的示例中，`Context` 属性显示在 `TableTemplate` 元素上，并应用于所有模板参数：</span><span class="sxs-lookup"><span data-stu-id="1ea45-122">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

## <a name="generic-typed-components"></a><span data-ttu-id="1ea45-123">泛型类型化组件</span><span class="sxs-lookup"><span data-stu-id="1ea45-123">Generic-typed components</span></span>

<span data-ttu-id="1ea45-124">模板化组件通常是泛型类型。</span><span class="sxs-lookup"><span data-stu-id="1ea45-124">Templated components are often generically typed.</span></span> <span data-ttu-id="1ea45-125">例如，泛型 `ListViewTemplate` 组件可用于呈现 `IEnumerable<T>` 值。</span><span class="sxs-lookup"><span data-stu-id="1ea45-125">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="1ea45-126">若要定义泛型组件，请使用 [`@typeparam`](xref:mvc/views/razor#typeparam) 指令指定类型参数：</span><span class="sxs-lookup"><span data-stu-id="1ea45-126">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="1ea45-127">使用泛型类型化组件时，将在可能的情况下推断类型参数：</span><span class="sxs-lookup"><span data-stu-id="1ea45-127">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="1ea45-128">否则，必须使用与类型参数的名称匹配的属性显式指定类型参数。</span><span class="sxs-lookup"><span data-stu-id="1ea45-128">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="1ea45-129">在下面的示例中，`TItem="Pet"` 指定类型：</span><span class="sxs-lookup"><span data-stu-id="1ea45-129">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```