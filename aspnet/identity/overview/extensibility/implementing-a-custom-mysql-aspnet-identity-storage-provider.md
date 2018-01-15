---
uid: identity/overview/extensibility/implementing-a-custom-mysql-aspnet-identity-storage-provider
title: "实现自定义 MySQL ASP.NET 标识存储提供程序 |Microsoft 文档"
author: raquelsa
description: "ASP.NET 标识是一种可扩展系统，以便您可以创建自己的存储提供程序并将其插入你的应用程序而无需重新使用应用程序..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/22/2015
ms.topic: article
ms.assetid: 248f5fe7-39ba-40ea-ab1e-71a69b0bd649
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /identity/overview/extensibility/implementing-a-custom-mysql-aspnet-identity-storage-provider
msc.type: authoredcontent
ms.openlocfilehash: 3bfbccd91705755fc24bb8305fff171baa26f370
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
<a name="implementing-a-custom-mysql-aspnet-identity-storage-provider"></a><span data-ttu-id="9be86-103">实现自定义 MySQL ASP.NET 标识存储提供程序</span><span class="sxs-lookup"><span data-stu-id="9be86-103">Implementing a Custom MySQL ASP.NET Identity Storage Provider</span></span>
====================
<span data-ttu-id="9be86-104">通过[Raquel Soares De Almeida](https://github.com/raquelsa)， [Suhas Joshi](https://github.com/suhasj)， [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="9be86-104">by [Raquel Soares De Almeida](https://github.com/raquelsa), [Suhas Joshi](https://github.com/suhasj), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="9be86-105">ASP.NET 标识是一种可扩展系统，以便您可以创建自己的存储提供程序并将其插入你的应用程序而无需重新使用该应用程序。</span><span class="sxs-lookup"><span data-stu-id="9be86-105">ASP.NET Identity is an extensible system which enables you to create your own storage provider and plug it into your application without re-working the application.</span></span> <span data-ttu-id="9be86-106">本主题介绍如何创建 ASP.NET 标识 MySQL 存储提供程序。</span><span class="sxs-lookup"><span data-stu-id="9be86-106">This topic describes how to create a MySQL storage provider for ASP.NET Identity.</span></span> <span data-ttu-id="9be86-107">有关创建自定义的存储提供程序的概述，请参阅[概述的自定义存储提供程序 ASP.NET 标识](overview-of-custom-storage-providers-for-aspnet-identity.md)。</span><span class="sxs-lookup"><span data-stu-id="9be86-107">For an overview of creating custom storage providers, see [Overview of Custom Storage Providers for ASP.NET Identity](overview-of-custom-storage-providers-for-aspnet-identity.md).</span></span>
> 
> <span data-ttu-id="9be86-108">若要完成本教程，你必须具有 Visual Studio 2013 Update 2。</span><span class="sxs-lookup"><span data-stu-id="9be86-108">To complete this tutorial, you must have Visual Studio 2013 with Update 2.</span></span>
> 
> <span data-ttu-id="9be86-109">本教程将：</span><span class="sxs-lookup"><span data-stu-id="9be86-109">This tutorial will:</span></span>
> 
> - <span data-ttu-id="9be86-110">演示如何在 Azure 上创建 MySQL 数据库实例。</span><span class="sxs-lookup"><span data-stu-id="9be86-110">Show how to create a MySQL database instance on Azure.</span></span>
> - <span data-ttu-id="9be86-111">演示如何使用 MySQL 客户端工具 (MySQL Workbench) 来创建表和管理你在 Azure 上的远程数据库。</span><span class="sxs-lookup"><span data-stu-id="9be86-111">Show how to use a MySQL client tool (MySQL Workbench) to create tables and manage your remote database on Azure.</span></span>
> - <span data-ttu-id="9be86-112">演示如何将替换了自定义实现上一个 MVC 应用程序项目的默认 ASP.NET 标识存储实现。</span><span class="sxs-lookup"><span data-stu-id="9be86-112">Show how to replace the default ASP.NET Identity storage implementation with our custom implementation on a MVC application project.</span></span>
> 
> <span data-ttu-id="9be86-113">本教程最初编写的 Raquel Soares De Almeida 和 Rick Anderson ( [ @RickAndMSFT ](https://twitter.com/#!/RickAndMSFT) )。</span><span class="sxs-lookup"><span data-stu-id="9be86-113">This tutorial was originally written by Raquel Soares De Almeida and Rick Anderson ( [@RickAndMSFT](https://twitter.com/#!/RickAndMSFT) ).</span></span> <span data-ttu-id="9be86-114">Suhas Joshi 已更新为使用标识 2.0 示例项目。</span><span class="sxs-lookup"><span data-stu-id="9be86-114">The sample project was updated for Identity 2.0 by Suhas Joshi.</span></span> <span data-ttu-id="9be86-115">Tom FitzMacken 已更新为使用标识 2.0 主题。</span><span class="sxs-lookup"><span data-stu-id="9be86-115">The topic was updated for Identity 2.0 by Tom FitzMacken.</span></span>


## <a name="download-completed-project"></a><span data-ttu-id="9be86-116">已完成的下载项目</span><span class="sxs-lookup"><span data-stu-id="9be86-116">Download completed project</span></span>

<span data-ttu-id="9be86-117">在本教程结束时，你将具有与使用在 Azure 上托管的 MySQL 数据库的 ASP.NET Identity 的 MVC 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="9be86-117">At the end of this tutorial, you will have an MVC application project with ASP.NET Identity working with a MySQL database hosted on Azure.</span></span>

<span data-ttu-id="9be86-118">你可以下载处的已完成的 MySQL 存储提供程序[AspNet.Identity.MySQL (CodePlex)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/)。</span><span class="sxs-lookup"><span data-stu-id="9be86-118">You can download the completed MySQL storage provider at [AspNet.Identity.MySQL (CodePlex)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/).</span></span>

## <a name="the-steps-you-will-perform"></a><span data-ttu-id="9be86-119">您将执行的步骤</span><span class="sxs-lookup"><span data-stu-id="9be86-119">The steps you will perform</span></span>

<span data-ttu-id="9be86-120">在本教程中，你将：</span><span class="sxs-lookup"><span data-stu-id="9be86-120">In this tutorial you will:</span></span>

1. <span data-ttu-id="9be86-121">在 Azure 上创建 MySQL 数据库</span><span class="sxs-lookup"><span data-stu-id="9be86-121">Create a MySQL database on Azure</span></span>
2. <span data-ttu-id="9be86-122">在 MySQL 中创建 ASP.NET 标识表</span><span class="sxs-lookup"><span data-stu-id="9be86-122">Create the ASP.NET Identity tables in MySQL</span></span>
3. <span data-ttu-id="9be86-123">创建 MVC 应用程序并将其配置为使用 MySQL 提供程序</span><span class="sxs-lookup"><span data-stu-id="9be86-123">Create an MVC application and configure it to use the MySQL provider</span></span>
4. <span data-ttu-id="9be86-124">运行应用</span><span class="sxs-lookup"><span data-stu-id="9be86-124">Run the app</span></span>

<span data-ttu-id="9be86-125">本主题不涉及 ASP.NET 标识和时实现客户存储提供程序必须做出的决策的体系结构。</span><span class="sxs-lookup"><span data-stu-id="9be86-125">This topic does not cover the architecture of ASP.NET Identity and the decisions you must make when implementing a customer storage provider.</span></span> <span data-ttu-id="9be86-126">该信息，请参阅[概述的自定义存储提供程序 ASP.NET 标识](overview-of-custom-storage-providers-for-aspnet-identity.md)。</span><span class="sxs-lookup"><span data-stu-id="9be86-126">For that information, see [Overview of Custom Storage Providers for ASP.NET Identity](overview-of-custom-storage-providers-for-aspnet-identity.md).</span></span>

## <a name="review-mysql-storage-provider-classes"></a><span data-ttu-id="9be86-127">查看 MySQL 存储提供程序类</span><span class="sxs-lookup"><span data-stu-id="9be86-127">Review MySQL storage provider classes</span></span>

<span data-ttu-id="9be86-128">之前跳转到创建 MySQL 存储提供程序的步骤，让我们看一下组成的存储提供程序的类。</span><span class="sxs-lookup"><span data-stu-id="9be86-128">Before jumping into the steps to create the MySQL storage provider, let's look at the classes that make up the storage provider.</span></span> <span data-ttu-id="9be86-129">你将需要管理的数据库操作的类和从应用程序来管理用户和角色调用的类。</span><span class="sxs-lookup"><span data-stu-id="9be86-129">You will need classes that manage the database operations and classes that are called from the application to manage users and roles.</span></span>

### <a name="storage-classes"></a><span data-ttu-id="9be86-130">存储类</span><span class="sxs-lookup"><span data-stu-id="9be86-130">Storage classes</span></span>

- <span data-ttu-id="9be86-131">[IdentityUser](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/IdentityUser.cs) -包含用户属性。</span><span class="sxs-lookup"><span data-stu-id="9be86-131">[IdentityUser](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/IdentityUser.cs) - contains properties for the user.</span></span>
- <span data-ttu-id="9be86-132">[UserStore](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserStore.cs) -包含用于添加、 更新或检索用户的操作。</span><span class="sxs-lookup"><span data-stu-id="9be86-132">[UserStore](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserStore.cs) - contains operations for adding, updating or retrieving users.</span></span>
- <span data-ttu-id="9be86-133">[IdentityRole](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/IdentityRole.cs) -包含角色的属性。</span><span class="sxs-lookup"><span data-stu-id="9be86-133">[IdentityRole](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/IdentityRole.cs) - contains properties for roles.</span></span>
- <span data-ttu-id="9be86-134">[RoleStore](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/RoleStore.cs) -包含用于添加、 删除、 更新和检索角色的操作。</span><span class="sxs-lookup"><span data-stu-id="9be86-134">[RoleStore](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/RoleStore.cs) - contains operations for adding, deleting, updating and retrieving roles.</span></span>

### <a name="data-access-layer-classes"></a><span data-ttu-id="9be86-135">数据访问层类</span><span class="sxs-lookup"><span data-stu-id="9be86-135">Data access layer classes</span></span>

<span data-ttu-id="9be86-136">对于此示例中，数据访问层类包含用于处理表; 的 SQL 语句但是，在代码中你可能想要使用如实体框架或 nhibernate 进行对象关系映射 (ORM)。</span><span class="sxs-lookup"><span data-stu-id="9be86-136">For this example, the data access layer classes contain SQL statements for working with the tables; however, in your code you might want to use object-relational mapping (ORM) such as Entity Framework or NHibernate.</span></span> <span data-ttu-id="9be86-137">具体而言，你的应用程序可能会遇到不包括延迟加载和对象缓存 ORM 的情况下的性能不佳。</span><span class="sxs-lookup"><span data-stu-id="9be86-137">In particular, your application may experience poor performance without an ORM that includes lazy loading and object caching.</span></span> <span data-ttu-id="9be86-138">有关详细信息，请参阅[而无需实体框架的 ASP.NET 标识 2.0？](https://aspnetidentity.codeplex.com/discussions/561828)</span><span class="sxs-lookup"><span data-stu-id="9be86-138">For more information, see [ASP.NET Identity 2.0 without Entity Framework?](https://aspnetidentity.codeplex.com/discussions/561828)</span></span>

- <span data-ttu-id="9be86-139">[MySQLDatabase](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/MySQLDatabase.cs) -包含的 MySQL 数据库连接和执行数据库操作的方法。</span><span class="sxs-lookup"><span data-stu-id="9be86-139">[MySQLDatabase](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/MySQLDatabase.cs) - contains the MySQL database connection and methods for performing database operations.</span></span> <span data-ttu-id="9be86-140">UserStore 和 RoleStore 同时实例化与此类的实例。</span><span class="sxs-lookup"><span data-stu-id="9be86-140">UserStore and RoleStore are both instantiated with an instance of this class.</span></span>
- <span data-ttu-id="9be86-141">[RoleTable](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/RoleTable.cs) -包含存储角色的表的数据库操作。</span><span class="sxs-lookup"><span data-stu-id="9be86-141">[RoleTable](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/RoleTable.cs) - contains database operations for the table that stores roles.</span></span>
- <span data-ttu-id="9be86-142">[UserClaimsTable](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserClaimsTable.cs) -包含，存储用户声明的表的数据库操作。</span><span class="sxs-lookup"><span data-stu-id="9be86-142">[UserClaimsTable](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserClaimsTable.cs) - contains database operations for the table that stores user claims.</span></span>
- <span data-ttu-id="9be86-143">[UserLoginsTable](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserLoginsTable.cs) -包含，存储用户登录信息的表的数据库操作。</span><span class="sxs-lookup"><span data-stu-id="9be86-143">[UserLoginsTable](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserLoginsTable.cs) - contains database operations for the table that stores user login information.</span></span>
- <span data-ttu-id="9be86-144">[UserRoleTable](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserRoleTable.cs) -包含用于将存储分配给哪些角色的用户表的数据库操作。</span><span class="sxs-lookup"><span data-stu-id="9be86-144">[UserRoleTable](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserRoleTable.cs) - contains database operations for the table that stores which users are assigned to which roles.</span></span>
- <span data-ttu-id="9be86-145">[数据库](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserTable.cs)-包含，存储用户的表的数据库操作。</span><span class="sxs-lookup"><span data-stu-id="9be86-145">[UserTable](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserTable.cs) - contains database operations for the table that stores users.</span></span>

## <a name="create-a-mysql-database-instance-on-azure"></a><span data-ttu-id="9be86-146">在 Azure 上创建 MySQL 数据库实例</span><span class="sxs-lookup"><span data-stu-id="9be86-146">Create a MySQL database instance on Azure</span></span>

1. <span data-ttu-id="9be86-147">登录到[Azure 门户](https://manage.windowsazure.com/)。</span><span class="sxs-lookup"><span data-stu-id="9be86-147">Log in to the [Azure Portal](https://manage.windowsazure.com/).</span></span>
2. <span data-ttu-id="9be86-148">单击**+ 新建**页上，，然后选择底部**存储**。</span><span class="sxs-lookup"><span data-stu-id="9be86-148">Click **+NEW** at the bottom of the page, and then select **STORE**.</span></span>  
    ![](implementing-a-custom-mysql-aspnet-identity-storage-provider/_static/image1.png)
3. <span data-ttu-id="9be86-149">在**选择和外接程序**向导中，选择**ClearDB MySQL 数据库**，然后单击右下角的对话框中的下一步箭头。</span><span class="sxs-lookup"><span data-stu-id="9be86-149">In the **Choose and Add-on** wizard, select **ClearDB MySQL Database** and click on the next arrow at the bottom right of the dialog.</span></span>  
    ![](implementing-a-custom-mysql-aspnet-identity-storage-provider/_static/image2.png)
4. <span data-ttu-id="9be86-150">保留默认值**免费**计划和更改**名称**到**IdentityMySQLDatabase**。</span><span class="sxs-lookup"><span data-stu-id="9be86-150">Keep the default **Free** plan and change the **Name** to **IdentityMySQLDatabase**.</span></span> <span data-ttu-id="9be86-151">选择离您最近的区域，然后单击下一步箭头。</span><span class="sxs-lookup"><span data-stu-id="9be86-151">Select the region nearest you and then click the next arrow.</span></span>  
    ![](implementing-a-custom-mysql-aspnet-identity-storage-provider/_static/image3.png)
5. <span data-ttu-id="9be86-152">单击复选标记以完成创建数据库。</span><span class="sxs-lookup"><span data-stu-id="9be86-152">Click the checkmark to complete the database creation.</span></span>  
    ![](implementing-a-custom-mysql-aspnet-identity-storage-provider/_static/image4.png)
6. <span data-ttu-id="9be86-153">创建你的数据库后，你可以管理从**外接程序**在管理门户中的选项卡。</span><span class="sxs-lookup"><span data-stu-id="9be86-153">After your database has been created, you can manage it from the **ADD-ONS** tab in the management portal.</span></span>   
    ![](implementing-a-custom-mysql-aspnet-identity-storage-provider/_static/image5.png)
7. <span data-ttu-id="9be86-154">你可以通过单击来获取的数据库连接信息**连接信息**位于页的底部。</span><span class="sxs-lookup"><span data-stu-id="9be86-154">You can get the database connection information by clicking on **CONNECTION INFO** at the bottom of the page.</span></span>  
    ![](implementing-a-custom-mysql-aspnet-identity-storage-provider/_static/image6.png)
8. <span data-ttu-id="9be86-155">通过单击复制按钮复制的连接字符串并将其保存，因此你可以使用更高版本在 MVC 应用程序。</span><span class="sxs-lookup"><span data-stu-id="9be86-155">Copy the connection string by clicking on the copy button and save it so you can use later in your MVC application.</span></span>   
    ![](implementing-a-custom-mysql-aspnet-identity-storage-provider/_static/image7.png)

## <a name="create-the-aspnet-identity-tables-in-a-mysql-database"></a><span data-ttu-id="9be86-156">在 MySQL 数据库中创建 ASP.NET 标识表</span><span class="sxs-lookup"><span data-stu-id="9be86-156">Create the ASP.NET Identity tables in a MySQL database</span></span>

### <a name="install-mysql-workbench-tool-to-connect-and-manage-mysql-database"></a><span data-ttu-id="9be86-157">安装 MySQL Workbench 工具连接和管理 MySQL 数据库</span><span class="sxs-lookup"><span data-stu-id="9be86-157">Install MySQL Workbench tool to connect and manage MySQL database</span></span>

1. <span data-ttu-id="9be86-158">安装**MySQL Workbench**工具[MySQL 下载页](http://dev.mysql.com/downloads/windows/installer/)</span><span class="sxs-lookup"><span data-stu-id="9be86-158">Install the **MySQL Workbench** tool from the [MySQL downloads page](http://dev.mysql.com/downloads/windows/installer/)</span></span>
2. <span data-ttu-id="9be86-159">启动应用并在上添加单击**MySQLConnections +**按钮以添加新连接。</span><span class="sxs-lookup"><span data-stu-id="9be86-159">Launch the app and add click on the **MySQLConnections +** button to add a new connection.</span></span> <span data-ttu-id="9be86-160">使用从本教程中前面创建的 Azure MySQL 数据库复制的连接字符串数据。</span><span class="sxs-lookup"><span data-stu-id="9be86-160">Use the connection string data you copied from the Azure MySQL database you created earlier in this tutorial.</span></span>
3. <span data-ttu-id="9be86-161">建立连接后，打开一个新**查询**选项卡上; 粘贴从命令[MySQLIdentity.sql](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/MySQLIdentity.sql)到查询并执行它才能创建数据库表。</span><span class="sxs-lookup"><span data-stu-id="9be86-161">After establishing the connection, open a new **Query** tab; paste the commands from [MySQLIdentity.sql](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/MySQLIdentity.sql) into the query and execute it in order to create the database tables.</span></span>
4. <span data-ttu-id="9be86-162">你现在具有所有如下所示，在 Azure 上托管的 MySQL 数据库上创建的 ASP.NET 标识所需表。</span><span class="sxs-lookup"><span data-stu-id="9be86-162">You now have all the ASP.NET Identity necessary tables created on a MySQL database hosted on Azure as shown below.</span></span>  
    ![](implementing-a-custom-mysql-aspnet-identity-storage-provider/_static/image1.jpg)

## <a name="create-an-mvc-application-project-from-template-and-configure-it-to-use-mysql-provider"></a><span data-ttu-id="9be86-163">从模板创建的 MVC 应用程序项目并将其配置为使用 MySQL 提供程序</span><span class="sxs-lookup"><span data-stu-id="9be86-163">Create an MVC application project from template and configure it to use MySQL provider</span></span>

<span data-ttu-id="9be86-164">如果需要安装[Visual Studio Express 2013 for Web](https://go.microsoft.com/fwlink/?LinkId=299058)或[Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=306566) Update 2。</span><span class="sxs-lookup"><span data-stu-id="9be86-164">If needed, install either [Visual Studio Express 2013 for Web](https://go.microsoft.com/fwlink/?LinkId=299058) or [Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=306566) with Update 2.</span></span>

### <a name="download-the-aspnetidentitymysql-project-from-codeplex"></a><span data-ttu-id="9be86-165">从 CodePlex 下载 ASP.NET.Identity.MySQL 项目</span><span class="sxs-lookup"><span data-stu-id="9be86-165">Download the ASP.NET.Identity.MySQL project from CodePlex</span></span>

1. <span data-ttu-id="9be86-166">浏览到存储库 URL [AspNet.Identity.MySQL (CodePlex)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/)。</span><span class="sxs-lookup"><span data-stu-id="9be86-166">Browse to the repository URL at [AspNet.Identity.MySQL (CodePlex)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/).</span></span>
2. <span data-ttu-id="9be86-167">下载的源代码。</span><span class="sxs-lookup"><span data-stu-id="9be86-167">Download the source code.</span></span>
3. <span data-ttu-id="9be86-168">将.zip 文件解压缩到本地文件夹。</span><span class="sxs-lookup"><span data-stu-id="9be86-168">Extract the .zip file into a local folder.</span></span>
4. <span data-ttu-id="9be86-169">打开 AspNet.Identity.MySQL 解决方案并生成它。</span><span class="sxs-lookup"><span data-stu-id="9be86-169">Open the AspNet.Identity.MySQL solution and build it.</span></span>

### <a name="create-a-new-mvc-application-project-from-template"></a><span data-ttu-id="9be86-170">从模板创建新的 MVC 应用程序项目</span><span class="sxs-lookup"><span data-stu-id="9be86-170">Create a new MVC application project from template</span></span>

1. <span data-ttu-id="9be86-171">右键单击**AspNet.Identity.MySQL**解决方案和**添加**，**新项目**</span><span class="sxs-lookup"><span data-stu-id="9be86-171">Right click the **AspNet.Identity.MySQL** solution and **Add**, **New Project**</span></span>
2. <span data-ttu-id="9be86-172">在**添加新项目**对话框中，选择**Visual C#**在左侧，然后**Web** ，然后选择**ASP.NET Web 应用程序**。</span><span class="sxs-lookup"><span data-stu-id="9be86-172">In the **Add New Project** Dialog select **Visual C#** on the left, then **Web** and then select **ASP.NET Web Application**.</span></span> <span data-ttu-id="9be86-173">命名你的项目**IdentityMySQLDemo**; 然后单击确定。</span><span class="sxs-lookup"><span data-stu-id="9be86-173">Name your project **IdentityMySQLDemo**; and then click OK.</span></span>  
  
    ![](implementing-a-custom-mysql-aspnet-identity-storage-provider/_static/image2.jpg)
3. <span data-ttu-id="9be86-174">在**新建 ASP.NET 项目**对话框中，选择 MVC 模板中的使用默认选项 (包括**单个用户帐户**作为身份验证方法)，然后单击**确定**.![](implementing-a-custom-mysql-aspnet-identity-storage-provider/_static/image3.jpg)</span><span class="sxs-lookup"><span data-stu-id="9be86-174">In the **New ASP.NET Project** dialog, select the MVC template with the default options (that includes **Individual User Accounts** as authentication method) and click **OK**.![](implementing-a-custom-mysql-aspnet-identity-storage-provider/_static/image3.jpg)</span></span>
4. <span data-ttu-id="9be86-175">在解决方案资源管理器，右键单击 IdentityMySQLDemo 项目并选择**管理 NuGet 包**。</span><span class="sxs-lookup"><span data-stu-id="9be86-175">In Solution Explorer, right-click your IdentityMySQLDemo project and select **Manage NuGet Packages**.</span></span> <span data-ttu-id="9be86-176">在搜索文本框对话框中，键入**Identity.EntityFramework**。</span><span class="sxs-lookup"><span data-stu-id="9be86-176">In the search text box dialog, type **Identity.EntityFramework**.</span></span> <span data-ttu-id="9be86-177">在结果列表中选择此包，然后单击**卸载**。</span><span class="sxs-lookup"><span data-stu-id="9be86-177">Select this package in the list of results and click **Uninstall**.</span></span> <span data-ttu-id="9be86-178">系统将提示您要卸载依赖项包 EntityFramework。</span><span class="sxs-lookup"><span data-stu-id="9be86-178">You will be prompted to uninstall the dependency package EntityFramework.</span></span> <span data-ttu-id="9be86-179">单击是我们无法再将此应用程序上的此包。</span><span class="sxs-lookup"><span data-stu-id="9be86-179">Click on Yes as we will no longer this package on this application.</span></span>
5. <span data-ttu-id="9be86-180">右键单击 IdentityMySQLDemo 项目中，选择**添加**，**的引用、 解决方案、 项目;**选择 AspNet.Identity.MySQL 项目，然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="9be86-180">Right click the IdentityMySQLDemo project, select **Add**, **Reference, Solution, Projects;** select the AspNet.Identity.MySQL project and click **OK**.</span></span>
6. <span data-ttu-id="9be86-181">在 IdentityMySQLDemo 项目中，将对所有引用</span><span class="sxs-lookup"><span data-stu-id="9be86-181">In the IdentityMySQLDemo project, replace all references to</span></span>  
    `using Microsoft.AspNet.Identity.EntityFramework;`  
 <span data-ttu-id="9be86-182">替换为</span><span class="sxs-lookup"><span data-stu-id="9be86-182">with</span></span>  
     `using AspNet.Identity.MySQL;`
7. <span data-ttu-id="9be86-183">在 IdentityModels.cs，集中**ApplicationDbContext**为派生自**MySqlDatabase**和包括构造函数采用一个参数使用的连接名称。</span><span class="sxs-lookup"><span data-stu-id="9be86-183">In IdentityModels.cs, set **ApplicationDbContext** to derive from **MySqlDatabase** and include a contructor that take a single parameter with the connection name.</span></span>  

    [!code-csharp[Main](implementing-a-custom-mysql-aspnet-identity-storage-provider/samples/sample1.cs)]
8. <span data-ttu-id="9be86-184">打开 IdentityConfig.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="9be86-184">Open the IdentityConfig.cs file.</span></span> <span data-ttu-id="9be86-185">在**ApplicationUserManager.Create**方法中，替换实例 UserManager 化替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="9be86-185">In the **ApplicationUserManager.Create** method, replace instantiating UserManager with the following code:</span></span>  

    [!code-csharp[Main](implementing-a-custom-mysql-aspnet-identity-storage-provider/samples/sample2.cs)]
9. <span data-ttu-id="9be86-186">打开 web.config 文件并将 DefaultConnection 字符串替换为此项将突出显示的值替换为你在前面的步骤创建的 MySQL 数据库的连接字符串：</span><span class="sxs-lookup"><span data-stu-id="9be86-186">Open the web.config file and replace the DefaultConnection string with this entry replacing the highlighted values with the connection string of the MySQL database you created on previous steps:</span></span>  

    [!code-xml[Main](implementing-a-custom-mysql-aspnet-identity-storage-provider/samples/sample3.xml?highlight=2)]

## <a name="run-the-app-and-connect-to-the-mysql-db"></a><span data-ttu-id="9be86-187">运行应用并连接到 MySQL 数据库</span><span class="sxs-lookup"><span data-stu-id="9be86-187">Run the app and connect to the MySQL DB</span></span>

1. <span data-ttu-id="9be86-188">右键单击**IdentityMySQLDemo**项目，然后选择**设为启动项目**</span><span class="sxs-lookup"><span data-stu-id="9be86-188">Right click the **IdentityMySQLDemo** project and select **Set as Startup Project**</span></span>
2. <span data-ttu-id="9be86-189">按**Ctrl + F5**生成并运行应用程序。</span><span class="sxs-lookup"><span data-stu-id="9be86-189">Press **Ctrl + F5** to build and run the app.</span></span>
3. <span data-ttu-id="9be86-190">单击**注册**页面顶部的选项卡。</span><span class="sxs-lookup"><span data-stu-id="9be86-190">Click on **Register** tab on the top of the page.</span></span>
4. <span data-ttu-id="9be86-191">输入新的用户名和密码，然后单击**注册**。</span><span class="sxs-lookup"><span data-stu-id="9be86-191">Enter a new user name and password and then click on **Register**.</span></span>  
  
    ![](implementing-a-custom-mysql-aspnet-identity-storage-provider/_static/image8.png)
5. <span data-ttu-id="9be86-192">现在，新的用户已注册，并登录。</span><span class="sxs-lookup"><span data-stu-id="9be86-192">The new user is now registered and logged in.</span></span>  
  
    ![](implementing-a-custom-mysql-aspnet-identity-storage-provider/_static/image9.png)
6. <span data-ttu-id="9be86-193">返回到 MySQL Workbench 工具并检查**IdentityMySQLDatabase**表的内容。</span><span class="sxs-lookup"><span data-stu-id="9be86-193">Go back to the MySQL Workbench tool and inspect the **IdentityMySQLDatabase** table's contents.</span></span> <span data-ttu-id="9be86-194">注册新用户，请检查项的用户表。</span><span class="sxs-lookup"><span data-stu-id="9be86-194">Inspect the users table for the entries as you register new users.</span></span>  
  
    ![](implementing-a-custom-mysql-aspnet-identity-storage-provider/_static/image10.png)

## <a name="next-steps"></a><span data-ttu-id="9be86-195">后续步骤</span><span class="sxs-lookup"><span data-stu-id="9be86-195">Next Steps</span></span>

<span data-ttu-id="9be86-196">有关如何启用此应用程序的其他身份验证方法的详细信息，请参阅[创建 ASP.NET MVC 5 应用程序使用 Facebook 和 Google OAuth2 和 OpenID 登录](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md)。</span><span class="sxs-lookup"><span data-stu-id="9be86-196">For more information on how to enable other authentication methods on this app, refer to [Create an ASP.NET MVC 5 App with Facebook and Google OAuth2 and OpenID Sign-on](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md).</span></span>

<span data-ttu-id="9be86-197">若要了解如何使用 OAuth 集成你的数据库以及如何设置用户访问权限限制给你的应用程序的角色，请参阅[将包含成员资格、 OAuth 和 SQL 数据库的安全 ASP.NET MVC 5 应用程序部署到 Azure](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data)。</span><span class="sxs-lookup"><span data-stu-id="9be86-197">To learn how to integrate your DB with OAuth and to set up roles to limit users access to your app, see [Deploy a Secure ASP.NET MVC 5 app with Membership, OAuth, and SQL Database to Azure](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data).</span></span>