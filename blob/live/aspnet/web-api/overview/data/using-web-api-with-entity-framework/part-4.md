---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-4
title: "La gestion des Relations d’entité | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/16/2014
ms.topic: article
ms.assetid: d2f5710c-23c7-40a5-9cd9-5d0516570cba
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-4
msc.type: authoredcontent
ms.openlocfilehash: 9294da7cd5b7a362d4ade9d1bf7e7747e20ee1a8
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="handling-entity-relations"></a><span data-ttu-id="916d2-102">Gestion des Relations d’entité</span><span class="sxs-lookup"><span data-stu-id="916d2-102">Handling Entity Relations</span></span>
====================
<span data-ttu-id="916d2-103">par [Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="916d2-103">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

[<span data-ttu-id="916d2-104">Télécharger le projet terminé</span><span class="sxs-lookup"><span data-stu-id="916d2-104">Download Completed Project</span></span>](https://github.com/MikeWasson/BookService)

<span data-ttu-id="916d2-105">Cette section décrit certains détails de comment EF charge les entités associées et comment gérer les propriétés de navigation circulaires dans vos classes de modèle.</span><span class="sxs-lookup"><span data-stu-id="916d2-105">This section describes some details of how EF loads related entities, and how to handle circular navigation properties in your model classes.</span></span> <span data-ttu-id="916d2-106">(Cette section fournit des connaissances générales et n’est pas nécessaire pour effectuer ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="916d2-106">(This section provides background knowledge, and is not required to complete the tutorial.</span></span> <span data-ttu-id="916d2-107">Si vous préférez, passez à le [partie 5.](part-5.md).)</span><span class="sxs-lookup"><span data-stu-id="916d2-107">If you prefer, skip to [Part 5.](part-5.md).)</span></span>

## <a name="eager-loading-versus-lazy-loading"></a><span data-ttu-id="916d2-108">Eager chargement par rapport à chargement différé</span><span class="sxs-lookup"><span data-stu-id="916d2-108">Eager Loading versus Lazy Loading</span></span>

<span data-ttu-id="916d2-109">Lorsque vous utilisez EF avec une base de données relationnelle, il est important de comprendre comment EF charge les données associées.</span><span class="sxs-lookup"><span data-stu-id="916d2-109">When using EF with a relational database, it's important to understand how EF loads related data.</span></span>

<span data-ttu-id="916d2-110">Il est également utile afficher les requêtes SQL qui génère d’EF.</span><span class="sxs-lookup"><span data-stu-id="916d2-110">It's also useful to see the SQL queries that EF generates.</span></span> <span data-ttu-id="916d2-111">Pour tracer le code SQL, ajoutez la ligne suivante de code pour le `BookServiceContext` constructeur :</span><span class="sxs-lookup"><span data-stu-id="916d2-111">To trace the SQL, add the following line of code to the `BookServiceContext` constructor:</span></span>

[!code-csharp[Main](part-4/samples/sample1.cs)]

<span data-ttu-id="916d2-112">Si vous envoyez une requête GET à /api/books, il retourne un JSON comme suit :</span><span class="sxs-lookup"><span data-stu-id="916d2-112">If you send a GET request to /api/books, it returns JSON like the following:</span></span>

[!code-console[Main](part-4/samples/sample2.cmd)]

<span data-ttu-id="916d2-113">Vous pouvez voir que la propriété Author est null, même si le catalogue contient une AuthorId valide.</span><span class="sxs-lookup"><span data-stu-id="916d2-113">You can see that the Author property is null, even though the book contains a valid AuthorId.</span></span> <span data-ttu-id="916d2-114">Est que EF ne charge pas les entités auteur associées.</span><span class="sxs-lookup"><span data-stu-id="916d2-114">That's because EF is not loading the related Author entities.</span></span> <span data-ttu-id="916d2-115">Confirme le journal de suivi de la requête SQL :</span><span class="sxs-lookup"><span data-stu-id="916d2-115">The trace log of the SQL query confirms this:</span></span>

[!code-console[Main](part-4/samples/sample3.sql)]

<span data-ttu-id="916d2-116">L’instruction SELECT prend à partir de la table de la documentation et ne fait pas référence à la table de l’auteur.</span><span class="sxs-lookup"><span data-stu-id="916d2-116">The SELECT statement takes from the Books table, and does not reference the Author table.</span></span>

<span data-ttu-id="916d2-117">Pour référence, voici la méthode dans la `BooksController` classe qui retourne la liste de livres.</span><span class="sxs-lookup"><span data-stu-id="916d2-117">For reference, here is the method in the `BooksController` class that returns the list of books.</span></span>

[!code-csharp[Main](part-4/samples/sample4.cs)]

<span data-ttu-id="916d2-118">Voyons comment nous pouvons retourner l’auteur en tant que partie des données JSON.</span><span class="sxs-lookup"><span data-stu-id="916d2-118">Let's see how we can return the Author as part of the JSON data.</span></span> <span data-ttu-id="916d2-119">Il existe trois façons de charger des données associées dans Entity Framework : chargement hâtif, chargement différé et chargement explicit.</span><span class="sxs-lookup"><span data-stu-id="916d2-119">There are three ways to load related data in Entity Framework: eager loading, lazy loading, and explicit loading.</span></span> <span data-ttu-id="916d2-120">Il existe des compromis avec chaque technique, il est donc important de comprendre comment elles fonctionnent.</span><span class="sxs-lookup"><span data-stu-id="916d2-120">There are trade-offs with each technique, so it's important to understand how they work.</span></span>

### <a name="eager-loading"></a><span data-ttu-id="916d2-121">Chargement hâtif</span><span class="sxs-lookup"><span data-stu-id="916d2-121">Eager Loading</span></span>

<span data-ttu-id="916d2-122">Avec *chargement hâtif*, EF charge les entités associées dans le cadre de la requête de base de données initiale.</span><span class="sxs-lookup"><span data-stu-id="916d2-122">With *eager loading*, EF loads related entities as part of the initial database query.</span></span> <span data-ttu-id="916d2-123">Pour effectuer un chargement hâtif, utilisez le **System.Data.Entity.Include** méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="916d2-123">To perform eager loading, use the **System.Data.Entity.Include** extension method.</span></span>

[!code-csharp[Main](part-4/samples/sample5.cs)]

<span data-ttu-id="916d2-124">Cela indique à EF pour inclure les données de l’auteur dans la requête.</span><span class="sxs-lookup"><span data-stu-id="916d2-124">This tells EF to include the Author data in the query.</span></span> <span data-ttu-id="916d2-125">Si vous apportez cette modification, et exécuter l’application, les données JSON ressemblent à ceci :</span><span class="sxs-lookup"><span data-stu-id="916d2-125">If you make this change and run the app, now the JSON data looks like this:</span></span>

[!code-console[Main](part-4/samples/sample6.cmd)]

<span data-ttu-id="916d2-126">Le journal de suivi montre que EF effectuée une jointure sur les tables de livre et l’auteur.</span><span class="sxs-lookup"><span data-stu-id="916d2-126">The trace log shows that EF performed a join on the Book and Author tables.</span></span>

[!code-console[Main](part-4/samples/sample7.cmd)]

### <a name="lazy-loading"></a><span data-ttu-id="916d2-127">Chargement différé</span><span class="sxs-lookup"><span data-stu-id="916d2-127">Lazy Loading</span></span>

<span data-ttu-id="916d2-128">Chargement différé, EF automatiquement charge une entité associée lors de la propriété de navigation pour cette entité est déréférencée.</span><span class="sxs-lookup"><span data-stu-id="916d2-128">With lazy loading, EF automatically loads a related entity when the navigation property for that entity is dereferenced.</span></span> <span data-ttu-id="916d2-129">Pour activer le chargement différé, vérifiez la propriété de navigation virtuel.</span><span class="sxs-lookup"><span data-stu-id="916d2-129">To enable lazy loading, make the navigation property virtual.</span></span> <span data-ttu-id="916d2-130">Par exemple, dans la classe Book :</span><span class="sxs-lookup"><span data-stu-id="916d2-130">For example, in the Book class:</span></span>

[!code-csharp[Main](part-4/samples/sample8.cs?highlight=6)]

<span data-ttu-id="916d2-131">Examinons à présent le code suivant :</span><span class="sxs-lookup"><span data-stu-id="916d2-131">Now consider the following code:</span></span>

[!code-csharp[Main](part-4/samples/sample9.cs)]

<span data-ttu-id="916d2-132">Lorsque le chargement différé est activé, l’accès à la `Author` propriété `books[0]` provoque EF interroger la base de données de l’auteur.</span><span class="sxs-lookup"><span data-stu-id="916d2-132">When lazy loading is enabled, accessing the `Author` property on `books[0]` causes EF to query the database for the author.</span></span>

<span data-ttu-id="916d2-133">Chargement différé requiert plusieurs allers-retours de base de données, étant donné que EF envoie une requête chaque fois qu’il récupère une entité associée.</span><span class="sxs-lookup"><span data-stu-id="916d2-133">Lazy loading requires multiple database trips, because EF sends a query each time it retrieves a related entity.</span></span> <span data-ttu-id="916d2-134">En règle générale, vous souhaitez désactivé pour les objets que vous sérialisez de chargement différé.</span><span class="sxs-lookup"><span data-stu-id="916d2-134">Generally, you want lazy loading disabled for objects that you serialize.</span></span> <span data-ttu-id="916d2-135">Le sérialiseur doit lire toutes les propriétés sur le modèle, ce qui déclenche le chargement des entités associées.</span><span class="sxs-lookup"><span data-stu-id="916d2-135">The serializer has to read all of the properties on the model, which triggers loading the related entities.</span></span> <span data-ttu-id="916d2-136">Par exemple, voici les requêtes SQL lorsque EF sérialise la liste des livres avec chargement différé activé.</span><span class="sxs-lookup"><span data-stu-id="916d2-136">For example, here are the SQL queries when EF serializes the list of books with lazy loading enabled.</span></span> <span data-ttu-id="916d2-137">Vous pouvez voir que EF effectue trois requêtes distinctes pour les auteurs de trois.</span><span class="sxs-lookup"><span data-stu-id="916d2-137">You can see that EF makes three separate queries for the three authors.</span></span>

[!code-console[Main](part-4/samples/sample10.sql)]

<span data-ttu-id="916d2-138">Il existe toujours une fois lorsque vous pouvez souhaiter utiliser le chargement différé.</span><span class="sxs-lookup"><span data-stu-id="916d2-138">There are still times when you might want to use lazy loading.</span></span> <span data-ttu-id="916d2-139">Chargement hâtif risque EF générer une jointure très complexe.</span><span class="sxs-lookup"><span data-stu-id="916d2-139">Eager loading can cause EF to generate a very complex join.</span></span> <span data-ttu-id="916d2-140">Ou vous devrez peut-être les entités associées pour un petit sous-ensemble des données, et le chargement tardif serait plus efficace.</span><span class="sxs-lookup"><span data-stu-id="916d2-140">Or you might need related entities for a small subset of the data, and lazy loading would be more efficient.</span></span>

<span data-ttu-id="916d2-141">Une façon d’éviter les problèmes de sérialisation doit sérialiser des objets de transfert de données (DTO) au lieu d’objets d’entité.</span><span class="sxs-lookup"><span data-stu-id="916d2-141">One way to avoid serialization problems is to serialize data transfer objects (DTOs) instead of entity objects.</span></span> <span data-ttu-id="916d2-142">Je vais expliquer cette approche plus loin dans l’article.</span><span class="sxs-lookup"><span data-stu-id="916d2-142">I'll show this approach later in the article.</span></span>

### <a name="explicit-loading"></a><span data-ttu-id="916d2-143">Chargement explicite</span><span class="sxs-lookup"><span data-stu-id="916d2-143">Explicit Loading</span></span>

<span data-ttu-id="916d2-144">Chargement explicite est similaire à chargement différé, sauf que vous explicitement les données associées dans le code ; Il ne se produit automatiquement lorsque vous accédez à une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="916d2-144">Explicit loading is similar to lazy loading, except that you explicitly get the related data in code; it doesn't happen automatically when you access a navigation property.</span></span> <span data-ttu-id="916d2-145">Chargement explicite vous donne davantage de contrôle sur quand charger des données connexes, mais nécessite du code supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="916d2-145">Explicit loading gives you more control over when to load related data, but requires extra code.</span></span> <span data-ttu-id="916d2-146">Pour plus d’informations sur le chargement explicite, consultez [le chargement des entités associées](https://msdn.microsoft.com/en-us/data/jj574232#explicit).</span><span class="sxs-lookup"><span data-stu-id="916d2-146">For more information about explicit loading, see [Loading Related Entities](https://msdn.microsoft.com/en-us/data/jj574232#explicit).</span></span>

## <a name="navigation-properties-and-circular-references"></a><span data-ttu-id="916d2-147">Propriétés de navigation et les références circulaires</span><span class="sxs-lookup"><span data-stu-id="916d2-147">Navigation Properties and Circular References</span></span>

<span data-ttu-id="916d2-148">Lorsque j’ai défini les modèles de livre et auteur, j’ai défini une propriété de navigation sur le `Book` classe pour la relation de l’auteur du livre, mais vous n’avez pas défini une propriété de navigation dans l’autre direction.</span><span class="sxs-lookup"><span data-stu-id="916d2-148">When I defined the Book and Author models, I defined a navigation property on the `Book` class for the Book-Author relationship, but I did not define a navigation property in the other direction.</span></span>

<span data-ttu-id="916d2-149">Que se passe-t-il si vous ajoutez la propriété de navigation correspondant à la `Author` classe ?</span><span class="sxs-lookup"><span data-stu-id="916d2-149">What happens if you add the corresponding navigation property to the `Author` class?</span></span>

[!code-csharp[Main](part-4/samples/sample11.cs?highlight=7)]

<span data-ttu-id="916d2-150">Malheureusement, ceci pose un problème lorsque vous sérialisez les modèles.</span><span class="sxs-lookup"><span data-stu-id="916d2-150">Unfortunately, this creates a problem when you serialize the models.</span></span> <span data-ttu-id="916d2-151">Si vous chargez les données associées, il crée un graphique d’objet circulaire.</span><span class="sxs-lookup"><span data-stu-id="916d2-151">If you load the related data, it creates a circular object graph.</span></span>

![](part-4/_static/image1.png)

<span data-ttu-id="916d2-152">Lorsque le module de formatage JSON ou XML tente de sérialiser le graphique, elle lève une exception.</span><span class="sxs-lookup"><span data-stu-id="916d2-152">When the JSON or XML formatter tries to serialize the graph, it will throw an exception.</span></span> <span data-ttu-id="916d2-153">Les deux formateurs lever les messages d’exception différent.</span><span class="sxs-lookup"><span data-stu-id="916d2-153">The two formatters throw different exception messages.</span></span> <span data-ttu-id="916d2-154">Voici un exemple pour le formateur JSON :</span><span class="sxs-lookup"><span data-stu-id="916d2-154">Here is an example for the JSON formatter:</span></span>

[!code-console[Main](part-4/samples/sample12.cmd)]

<span data-ttu-id="916d2-155">Voici le formateur XML :</span><span class="sxs-lookup"><span data-stu-id="916d2-155">Here is the XML formatter:</span></span>

[!code-xml[Main](part-4/samples/sample13.xml)]

<span data-ttu-id="916d2-156">Une solution consiste à utiliser les DTO, je décris dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="916d2-156">One solution is to use DTOs, which I describe in the next section.</span></span> <span data-ttu-id="916d2-157">Vous pouvez également configurer les formateurs JSON et XML pour gérer les cycles de graphique.</span><span class="sxs-lookup"><span data-stu-id="916d2-157">Alternatively, you can configure the JSON and XML formatters to handle graph cycles.</span></span> <span data-ttu-id="916d2-158">Pour plus d’informations, consultez [la gestion des références d’objet circulaires](../../formats-and-model-binding/json-and-xml-serialization.md#handling_circular_object_references).</span><span class="sxs-lookup"><span data-stu-id="916d2-158">For more information, see [Handling Circular Object References](../../formats-and-model-binding/json-and-xml-serialization.md#handling_circular_object_references).</span></span>

<span data-ttu-id="916d2-159">Pour ce didacticiel, vous n’avez pas besoin du `Author.Book` propriété de navigation, donc vous pouvez la laisser.</span><span class="sxs-lookup"><span data-stu-id="916d2-159">For this tutorial, you don't need the `Author.Book` navigation property, so you can leave it out.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="916d2-160">[Précédent](part-3.md)
[Suivant](part-5.md)</span><span class="sxs-lookup"><span data-stu-id="916d2-160">[Previous](part-3.md)
[Next](part-5.md)</span></span>