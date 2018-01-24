---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application
title: "Implémentation de l’héritage avec Entity Framework 6 dans une Application ASP.NET MVC 5 (11 12) | Documents Microsoft"
author: tdykstra
description: "L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 5 à l’aide de l’Entity Framework 6 Code First et Visual Studio en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/07/2014
ms.topic: article
ms.assetid: 08834147-77ec-454a-bb7a-d931d2a40dab
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: e6ee3f9c055a15b13c27f94675006b9a7e804f1b
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="implementing-inheritance-with-the-entity-framework-6-in-an-aspnet-mvc-5-application-11-of-12"></a><span data-ttu-id="0b7bc-103">Implémentation de l’héritage avec Entity Framework 6 dans une Application ASP.NET MVC 5 (11 12)</span><span class="sxs-lookup"><span data-stu-id="0b7bc-103">Implementing Inheritance with the Entity Framework 6 in an ASP.NET MVC 5 Application (11 of 12)</span></span>
====================
<span data-ttu-id="0b7bc-104">Par [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="0b7bc-104">by [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="0b7bc-105">[Télécharger le projet terminé](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8) ou [télécharger le PDF](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20Entity%20Framework%206%20Code%20First%20using%20MVC%205.pdf)</span><span class="sxs-lookup"><span data-stu-id="0b7bc-105">[Download Completed Project](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8) or [Download PDF](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20Entity%20Framework%206%20Code%20First%20using%20MVC%205.pdf)</span></span>

> <span data-ttu-id="0b7bc-106">L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 5 à l’aide de l’Entity Framework 6 Code First et Visual Studio 2013.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-106">The Contoso University sample web application demonstrates how to create ASP.NET MVC 5 applications using the Entity Framework 6 Code First and Visual Studio 2013.</span></span> <span data-ttu-id="0b7bc-107">Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span><span class="sxs-lookup"><span data-stu-id="0b7bc-107">For information about the tutorial series, see [the first tutorial in the series](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span></span>


<span data-ttu-id="0b7bc-108">Dans le didacticiel précédent vous gérée des exceptions d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-108">In the previous tutorial you handled concurrency exceptions.</span></span> <span data-ttu-id="0b7bc-109">Ce didacticiel vous indiquera comment implémenter l’héritage dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-109">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="0b7bc-110">Dans la programmation orientée objet, vous pouvez utiliser [héritage](http://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming)) pour faciliter la [réutilisation du code](http://en.wikipedia.org/wiki/Code_reuse).</span><span class="sxs-lookup"><span data-stu-id="0b7bc-110">In object-oriented programming, you can use [inheritance](http://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming)) to facilitate [code reuse](http://en.wikipedia.org/wiki/Code_reuse).</span></span> <span data-ttu-id="0b7bc-111">Dans ce didacticiel, vous allez modifier le `Instructor` et `Student` afin qu’ils dérivent des classes un `Person` classe qui contient les propriétés de base `LastName` qui sont communs aux instructeurs et les étudiants.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-111">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="0b7bc-112">Vous ne seront pas ajouter ou modifier des pages web, mais vous allez modifier la partie du code et ces modifications sont répercutées automatiquement dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-112">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

## <a name="options-for-mapping-inheritance-to-database-tables"></a><span data-ttu-id="0b7bc-113">Options pour le mappage d’héritage pour les tables de base de données</span><span class="sxs-lookup"><span data-stu-id="0b7bc-113">Options for mapping inheritance to database tables</span></span>

<span data-ttu-id="0b7bc-114">Le `Instructor` et `Student` des classes dans le `School` modèle de données possèdent plusieurs propriétés qui sont identiques :</span><span class="sxs-lookup"><span data-stu-id="0b7bc-114">The `Instructor` and `Student` classes in the `School` data model have several properties that are identical:</span></span>

![Student_and_Instructor_classes](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

<span data-ttu-id="0b7bc-116">Supposons que vous souhaitez éliminer le code redondant pour les propriétés qui sont partagées par les `Instructor` et `Student` entités.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-116">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="0b7bc-117">Ou vous souhaitez écrire un service qui peut mettre en forme les noms sans soins si le nom provient d’un formateur ou un étudiant.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-117">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="0b7bc-118">Vous pouvez créer un `Person` classe qui contient uniquement les propriétés partagées de base, puis apportez les `Instructor` et `Student` entités héritent de cette classe de base, comme indiqué dans l’illustration suivante :</span><span class="sxs-lookup"><span data-stu-id="0b7bc-118">You could create a `Person` base class which contains only those shared properties, then make the `Instructor` and `Student` entities inherit from that base class, as shown in the following illustration:</span></span>

![Student_and_Instructor_classes_deriving_from_Person_class](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

<span data-ttu-id="0b7bc-120">Il existe plusieurs façons que cette structure d’héritage peut être représentée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-120">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="0b7bc-121">Vous pouvez avoir un `Person` table qui consacrée des informations sur les étudiants et instructeurs dans une table unique.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-121">You could have a `Person` table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="0b7bc-122">Certaines colonnes pourraient s’appliquent uniquement aux instructeurs (`HireDate`), certains uniquement pour les étudiants (`EnrollmentDate`), certaines pour les deux (`LastName`, `FirstName`).</span><span class="sxs-lookup"><span data-stu-id="0b7bc-122">Some of the columns could apply only to instructors (`HireDate`), some only to students (`EnrollmentDate`), some to both (`LastName`, `FirstName`).</span></span> <span data-ttu-id="0b7bc-123">En règle générale, vous devriez un *discriminateur* colonne afin d’indiquer le type de chaque ligne représente.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-123">Typically, you'd have a *discriminator* column to indicate which type each row represents.</span></span> <span data-ttu-id="0b7bc-124">Par exemple, la colonne de discriminateur peut-être « Formateur » pour les enseignants et « Étudiant » pour les étudiants.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-124">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![Table-par-hierarchy_example](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

<span data-ttu-id="0b7bc-126">Ce modèle de la génération d’une structure d’héritage d’entité à partir d’une table de base de données unique est appelé *table par hiérarchie* l’héritage (TPH).</span><span class="sxs-lookup"><span data-stu-id="0b7bc-126">This pattern of generating an entity inheritance structure from a single database table is called *table-per-hierarchy* (TPH) inheritance.</span></span>

<span data-ttu-id="0b7bc-127">Une alternative consiste à rendre la base de données ressemble plus à la structure d’héritage.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-127">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="0b7bc-128">Par exemple, vous pouvez avoir seulement les champs de nom dans la `Person` table et avez distinct `Instructor` et `Student` tables avec des champs de date.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-128">For example, you could have only the name fields in the `Person` table and have separate `Instructor` and `Student` tables with the date fields.</span></span>

![Table-par-type_inheritance](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)

<span data-ttu-id="0b7bc-130">Ce modèle de configuration d’une table de base de données pour chaque classe d’entité est appelé *table par type* l’héritage (TPT).</span><span class="sxs-lookup"><span data-stu-id="0b7bc-130">This pattern of making a database table for each entity class is called *table per type* (TPT) inheritance.</span></span>

<span data-ttu-id="0b7bc-131">Encore une autre option consiste à mapper tous les types non abstraits à des tables individuelles.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-131">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="0b7bc-132">Toutes les propriétés d’une classe, y compris les propriétés héritées, mappent aux colonnes de la table correspondante.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-132">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="0b7bc-133">Ce modèle est appelé l’héritage de Table-par classe concrète (TPC).</span><span class="sxs-lookup"><span data-stu-id="0b7bc-133">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="0b7bc-134">Si vous avez implémenté l’héritage TPC pour le `Person`, `Student`, et `Instructor` classes comme indiqué précédemment, le `Student` et `Instructor` tables ressemble pas différents après l’implémentation de l’héritage de leur.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-134">If you implemented TPC inheritance for the `Person`, `Student`, and `Instructor` classes as shown earlier, the `Student` and `Instructor` tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="0b7bc-135">TPC et modèles d’héritage TPH généralement offrent de meilleures performances dans Entity Framework que les modèles d’héritage TPT, étant donné que les modèles TPT peuvent entraîner des requêtes de jointure complexe.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-135">TPC and TPH inheritance patterns generally deliver better performance in the Entity Framework than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="0b7bc-136">Ce didacticiel montre comment implémenter l’héritage TPH.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-136">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="0b7bc-137">TPH étant le modèle d’héritage dans Entity Framework, il vous est de créer un `Person` de classe, de modifier le `Instructor` et `Student` comme classes de dérivation `Person`, ajouter la nouvelle classe par le `DbContext`et créer un migration.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-137">TPH is the default inheritance pattern in the Entity Framework, so all you have to do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span> <span data-ttu-id="0b7bc-138">(Pour plus d’informations sur la façon d’implémenter les autres modèles d’héritage, consultez [mappage de l’héritage Table par Type (TPT)](https://msdn.microsoft.com/en-us/data/jj591617#2.5) et [mappage de l’héritage de classe concrète-par-Table (TPC)](https://msdn.microsoft.com/en-us/data/jj591617#2.6) sur le site MSDN Entity Framework documentation sur.)</span><span class="sxs-lookup"><span data-stu-id="0b7bc-138">(For information about how to implement the other inheritance patterns, see [Mapping the Table-Per-Type (TPT) Inheritance](https://msdn.microsoft.com/en-us/data/jj591617#2.5) and [Mapping the Table-Per-Concrete Class (TPC) Inheritance](https://msdn.microsoft.com/en-us/data/jj591617#2.6) in the MSDN Entity Framework documentation.)</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="0b7bc-139">Créer la classe de personne</span><span class="sxs-lookup"><span data-stu-id="0b7bc-139">Create the Person class</span></span>

<span data-ttu-id="0b7bc-140">Dans le *modèles* dossier, créez *Person.cs* et remplacez le code de modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="0b7bc-140">In the *Models* folder, create *Person.cs* and replace the template code with the following code:</span></span>

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

## <a name="make-student-and-instructor-classes-inherit-from-person"></a><span data-ttu-id="0b7bc-141">Faites en sorte que les classes de Student et Instructor hérite de personne</span><span class="sxs-lookup"><span data-stu-id="0b7bc-141">Make Student and Instructor classes inherit from Person</span></span>

<span data-ttu-id="0b7bc-142">Dans *Instructor.cs*, dériver le `Instructor` classe à partir de la `Person` classe et supprimez les champs de clé et le nom.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-142">In *Instructor.cs*, derive the `Instructor` class from the `Person` class and remove the key and name fields.</span></span> <span data-ttu-id="0b7bc-143">Le code doit ressembler à l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="0b7bc-143">The code will look like the following example:</span></span>

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

<span data-ttu-id="0b7bc-144">Apporter des modifications similaires à *Student.cs*.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-144">Make similar changes to *Student.cs*.</span></span> <span data-ttu-id="0b7bc-145">La `Student` classe doit ressembler à l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="0b7bc-145">The `Student` class will look like the following example:</span></span>

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs)]

## <a name="add-the-person-entity-type-to-the-model"></a><span data-ttu-id="0b7bc-146">Ajouter le Type d’entité personne au modèle</span><span class="sxs-lookup"><span data-stu-id="0b7bc-146">Add the Person Entity Type to the Model</span></span>

<span data-ttu-id="0b7bc-147">Dans *SchoolContext.cs*, ajoutez un `DbSet` propriété pour la `Person` type d’entité :</span><span class="sxs-lookup"><span data-stu-id="0b7bc-147">In *SchoolContext.cs*, add a `DbSet` property for the `Person` entity type:</span></span>

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

<span data-ttu-id="0b7bc-148">C’est tout ce qui a besoin d’Entity Framework pour configurer l’héritage table par hiérarchie.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-148">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="0b7bc-149">Comme vous le verrez, lorsque la base de données est mise à jour, il aura un `Person` de table à la place de la `Student` et `Instructor` tables.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-149">As you'll see, when the database is updated, it will have a `Person` table in place of the `Student` and `Instructor` tables.</span></span>

## <a name="create-and-update-a-migrations-file"></a><span data-ttu-id="0b7bc-150">Créer et mettre à jour un fichier de migration</span><span class="sxs-lookup"><span data-stu-id="0b7bc-150">Create and Update a Migrations File</span></span>

<span data-ttu-id="0b7bc-151">Dans Package Manager Console (PMC), entrez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="0b7bc-151">In the Package Manager Console (PMC), enter the following command:</span></span>

`Add-Migration Inheritance`

<span data-ttu-id="0b7bc-152">Exécuter le `Update-Database` dans PMC.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-152">Run the `Update-Database` command in the PMC.</span></span> <span data-ttu-id="0b7bc-153">La commande échoue à ce stade parce que nous avons des données existantes migrations ne sait pas comment gérer.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-153">The command will fail at this point because we have existing data that migrations doesn't know how to handle.</span></span> <span data-ttu-id="0b7bc-154">Vous obtenez un message d’erreur tel que le suivant :</span><span class="sxs-lookup"><span data-stu-id="0b7bc-154">You get an error message like the following one:</span></span>

> <span data-ttu-id="0b7bc-155">*Impossible de supprimer l’objet ' dbo. Formateur ', car elle est référencée par une contrainte FOREIGN KEY.*</span><span class="sxs-lookup"><span data-stu-id="0b7bc-155">*Could not drop object 'dbo.Instructor' because it is referenced by a FOREIGN KEY constraint.*</span></span>


<span data-ttu-id="0b7bc-156">Ouvrez *Migrations\&lt ; horodatage&gt;\_Inheritance.cs* et remplacez le `Up` méthode avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="0b7bc-156">Open *Migrations\&lt;timestamp&gt;\_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

<span data-ttu-id="0b7bc-157">Ce code prend en charge les tâches de mise à jour de base de données suivantes :</span><span class="sxs-lookup"><span data-stu-id="0b7bc-157">This code takes care of the following database update tasks:</span></span>

- <span data-ttu-id="0b7bc-158">Supprime les contraintes de clé étrangère et les index qui pointent vers la table d’étudiants.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-158">Removes foreign key constraints and indexes that point to the Student table.</span></span>
- <span data-ttu-id="0b7bc-159">Renomme la table de formateurs en tant que personne et apporte les modifications nécessaires pour stocker les données de l’étudiant :</span><span class="sxs-lookup"><span data-stu-id="0b7bc-159">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

    - <span data-ttu-id="0b7bc-160">Ajoute EnrollmentDate nullable pour les étudiants.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-160">Adds nullable EnrollmentDate for students.</span></span>
    - <span data-ttu-id="0b7bc-161">Ajoute la colonne de discriminateur pour indiquer si une ligne est pour un étudiant ou un formateur.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-161">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>
    - <span data-ttu-id="0b7bc-162">Rend HireDate nullable étant donné que les lignes de l’étudiant n’ont des dates d’embauche.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-162">Makes HireDate nullable since student rows won't have hire dates.</span></span>
    - <span data-ttu-id="0b7bc-163">Ajoute un champ temporaire qui sera utilisé pour mettre à jour les clés étrangères qui pointent vers les étudiants.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-163">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="0b7bc-164">Lorsque vous copiez des étudiants dans la table Person ils obtenez de nouvelles valeurs de clé primaires.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-164">When you copy students into the Person table they'll get new primary key values.</span></span>
- <span data-ttu-id="0b7bc-165">Copie des données à partir de la table de l’étudiant dans la table Person.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-165">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="0b7bc-166">Cela provoque des étudiants obtenir attribué les nouvelles valeurs de clé primaires.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-166">This causes students to get assigned new primary key values.</span></span>
- <span data-ttu-id="0b7bc-167">Résout des valeurs de clés étrangères qui pointent vers les étudiants.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-167">Fixes foreign key values that point to students.</span></span>
- <span data-ttu-id="0b7bc-168">Crée de nouveau les contraintes de clé étrangère et des index, désormais de les utiliser pour la table Person.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-168">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="0b7bc-169">(Si vous aviez utilisé des GUID au lieu de l’entier en tant que le type de clé primaire, les valeurs de clé primaire étudiant n’ont pas à modifier, et plusieurs de ces étapes a été omises).</span><span class="sxs-lookup"><span data-stu-id="0b7bc-169">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="0b7bc-170">Exécutez le `update-database` réexécutez la commande.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-170">Run the `update-database` command again.</span></span>

<span data-ttu-id="0b7bc-171">(Dans un système de production vous fera modifications correspondantes apportées à la méthode vers le bas dans le cas où vous deviez jamais qui permet de revenir à la version précédente de la base de données.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-171">(In a production system you would make corresponding changes to the Down method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="0b7bc-172">Pour ce didacticiel vous n’utiliserez pas la méthode vers le bas.)</span><span class="sxs-lookup"><span data-stu-id="0b7bc-172">For this tutorial you won't be using the Down method.)</span></span>

> [!NOTE]
> <span data-ttu-id="0b7bc-173">Il est possible d’obtenir d’autres erreurs lors de la migration des données et apporter des modifications de schéma.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-173">It's possible to get other errors when migrating data and making schema changes.</span></span> <span data-ttu-id="0b7bc-174">Si vous obtenez des erreurs de migration vous ne peut pas résoudre, vous pouvez continuer avec le didacticiel en modifiant la chaîne de connexion dans le *Web.config* de fichiers ou en supprimant la base de données.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-174">If you get migration errors you can't resolve, you can continue with the tutorial by changing the connection string in the *Web.config* file or by deleting the database.</span></span> <span data-ttu-id="0b7bc-175">L’approche la plus simple consiste à renommer la base de données dans le *Web.config* fichier.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-175">The simplest approach is to rename the database in the *Web.config* file.</span></span> <span data-ttu-id="0b7bc-176">Par exemple, modifiez le nom de la base de données à ContosoUniversity2 comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="0b7bc-176">For example, change the database name to ContosoUniversity2 as shown in the following example:</span></span>
> 
> [!code-xml[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.xml?highlight=2)]
> 
> <span data-ttu-id="0b7bc-177">Avec une base de données, il n’existe pas de données à migrer et le `update-database` commande est beaucoup plus susceptible de se terminer sans erreur.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-177">With a new database, there is no data to migrate, and the `update-database` command is much more likely to complete without errors.</span></span> <span data-ttu-id="0b7bc-178">Pour obtenir des instructions sur la suppression de la base de données, consultez [le déplacement d’une base de données à partir de Visual Studio 2012](http://romiller.com/2013/05/17/how-to-drop-a-database-from-visual-studio-2012/).</span><span class="sxs-lookup"><span data-stu-id="0b7bc-178">For instructions on how to delete the database, see [How to Drop a Database from Visual Studio 2012](http://romiller.com/2013/05/17/how-to-drop-a-database-from-visual-studio-2012/).</span></span> <span data-ttu-id="0b7bc-179">Si vous adoptez cette approche pour pouvoir continuer avec le didacticiel, ignorez l’étape de déploiement à la fin de ce didacticiel ou déployer un nouveau site et de la base de données.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-179">If you take this approach in order to continue with the tutorial, skip the deployment step at the end of this tutorial or deploy to a new site and database.</span></span> <span data-ttu-id="0b7bc-180">Si vous déployez une mise à jour sur le même site que vous avez été déploiement déjà, EF obtiendrez la même erreur lorsqu’elle s’exécute automatiquement les migrations.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-180">If you deploy an update to the same site you've been deploying to already, EF will get the same error there when it runs migrations automatically.</span></span> <span data-ttu-id="0b7bc-181">Si vous souhaitez corriger une erreur de migration, cette ressource est un des forums d’Entity Framework ou StackOverflow.com.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-181">If you want to troubleshoot a migrations error, the best resource is one of the Entity Framework forums or StackOverflow.com.</span></span>


## <a name="testing"></a><span data-ttu-id="0b7bc-182">Test</span><span class="sxs-lookup"><span data-stu-id="0b7bc-182">Testing</span></span>

<span data-ttu-id="0b7bc-183">Exécuter le site et essayez différentes pages.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-183">Run the site and try various pages.</span></span> <span data-ttu-id="0b7bc-184">Tout fonctionne comme auparavant.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-184">Everything works the same as it did before.</span></span>

<span data-ttu-id="0b7bc-185">Dans **l’Explorateur de serveurs** développez **données Connections\SchoolContext** , puis **Tables**, et vous constatez que la **étudiant** et **Formateur** tables ont été remplacés par un **personne** table.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-185">In **Server Explorer,** expand **Data Connections\SchoolContext** and then **Tables**, and you see that the **Student** and **Instructor** tables have been replaced by a **Person** table.</span></span> <span data-ttu-id="0b7bc-186">Développez le **personne** table et que vous consultez dont toutes les colonnes utilisées dans les **Student** et **formateur** tables.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-186">Expand the **Person** table and you see that it has all of the columns that used to be in the **Student** and **Instructor** tables.</span></span>

![Server_Explorer_showing_Person_table](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image5.png)

<span data-ttu-id="0b7bc-188">Avec le bouton droit de la table Person, puis cliquez sur **afficher les données de Table** pour afficher la colonne de discriminateur.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-188">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

<span data-ttu-id="0b7bc-189">Le diagramme suivant illustre la structure de la base de données School :</span><span class="sxs-lookup"><span data-stu-id="0b7bc-189">The following diagram illustrates the structure of the new School database:</span></span>

![School_database_diagram](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image7.png)

## <a name="deploy-to-azure"></a><span data-ttu-id="0b7bc-191">Déployer vers Azure</span><span class="sxs-lookup"><span data-stu-id="0b7bc-191">Deploy to Azure</span></span>

<span data-ttu-id="0b7bc-192">Cette section, vous devez avoir terminé le paramètre facultatif **déploiement de l’application dans Azure** section [partie 3, tri, filtrage et la pagination](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md) de cette série de didacticiels.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-192">This section requires you to have completed the optional **Deploying the app to Azure** section in [Part 3, Sorting, Filtering, and Paging](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md) of this tutorial series.</span></span> <span data-ttu-id="0b7bc-193">Si vous aviez des erreurs de migrations résolus en supprimant la base de données dans votre projet local, ignorez cette étape ; ou créer un nouveau site et la base de données et les déployer vers le nouvel environnement.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-193">If you had migrations errors that you resolved by deleting the database in your local project, skip this step; or create a new site and database, and deploy to the new environment.</span></span>

1. <span data-ttu-id="0b7bc-194">Dans Visual Studio, cliquez sur le projet dans **l’Explorateur de solutions** et sélectionnez **publier** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-194">In Visual Studio, right-click the project in **Solution Explorer** and select **Publish** from the context menu.</span></span>  
  
    ![Publier dans le menu contextuel du projet](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image8.png)
2. <span data-ttu-id="0b7bc-196">Cliquez sur **Publier**.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-196">Click **Publish**.</span></span>  
  
    ![publish](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image9.png)  
  
 <span data-ttu-id="0b7bc-198">L’application Web s’ouvre dans votre navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-198">The Web app will open in your default browser.</span></span>
3. <span data-ttu-id="0b7bc-199">Tester l’application pour vérifier qu’il fonctionne.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-199">Test the application to verify it's working.</span></span>

    <span data-ttu-id="0b7bc-200">La première fois que vous exécutez une page qui accède à la base de données, Entity Framework exécute toutes les migrations `Up` méthodes nécessaires à la mise à jour avec le modèle de données actuel de la base de données.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-200">The first time you run a page that accesses the database, the Entity Framework runs all of the migrations `Up` methods required to bring the database up to date with the current data model.</span></span>

## <a name="summary"></a><span data-ttu-id="0b7bc-201">Résumé</span><span class="sxs-lookup"><span data-stu-id="0b7bc-201">Summary</span></span>

<span data-ttu-id="0b7bc-202">Vous avez implémenté l’héritage table par hiérarchie pour le `Person`, `Student`, et `Instructor` classes.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-202">You've implemented table-per-hierarchy inheritance for the `Person`, `Student`, and `Instructor` classes.</span></span> <span data-ttu-id="0b7bc-203">Pour plus d’informations sur cette modification et autres structures de l’héritage, consultez [modèle d’héritage TPT](https://msdn.microsoft.com/en-us/data/jj618293) et [modèle d’héritage TPH](https://msdn.microsoft.com/en-us/data/jj618292) sur MSDN.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-203">For more information about this and other inheritance structures, see [TPT Inheritance Pattern](https://msdn.microsoft.com/en-us/data/jj618293) and [TPH Inheritance Pattern](https://msdn.microsoft.com/en-us/data/jj618292) on MSDN.</span></span> <span data-ttu-id="0b7bc-204">Dans l’étape suivante du didacticiel, vous allez apprendre à gérer une variété de scénarios de Entity Framework relativement avancés.</span><span class="sxs-lookup"><span data-stu-id="0b7bc-204">In the next tutorial you'll see how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

<span data-ttu-id="0b7bc-205">Vous trouverez des liens vers d’autres ressources Entity Framework dans le [ASP.NET Data Access - ressources recommandées](../../../../whitepapers/aspnet-data-access-content-map.md).</span><span class="sxs-lookup"><span data-stu-id="0b7bc-205">Links to other Entity Framework resources can be found in the [ASP.NET Data Access - Recommended Resources](../../../../whitepapers/aspnet-data-access-content-map.md).</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="0b7bc-206">[Précédent](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)
[Suivant](advanced-entity-framework-scenarios-for-an-mvc-web-application.md)</span><span class="sxs-lookup"><span data-stu-id="0b7bc-206">[Previous](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)
[Next](advanced-entity-framework-scenarios-for-an-mvc-web-application.md)</span></span>