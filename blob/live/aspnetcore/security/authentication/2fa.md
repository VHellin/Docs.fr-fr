---
title: "Authentification à deux facteurs avec SMS"
author: rick-anderson
description: "Montre comment configurer une authentification à deux facteurs (2FA) avec ASP.NET Core"
ms.author: riande
manager: wpickett
ms.date: 08/15/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/2fa
ms.openlocfilehash: 0970b7e95b116ceab1d17502d2f8aee9cd821715
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="two-factor-authentication-with-sms"></a><span data-ttu-id="a04ad-103">Authentification à deux facteurs avec SMS</span><span class="sxs-lookup"><span data-stu-id="a04ad-103">Two-factor authentication with SMS</span></span>

<span data-ttu-id="a04ad-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [développeurs-Suisse](https://github.com/Swiss-Devs)</span><span class="sxs-lookup"><span data-stu-id="a04ad-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

<span data-ttu-id="a04ad-105">Ce didacticiel s’applique à ASP.NET Core 1.x uniquement.</span><span class="sxs-lookup"><span data-stu-id="a04ad-105">This tutorial applies to ASP.NET Core 1.x only.</span></span> <span data-ttu-id="a04ad-106">Consultez [génération permettant un Code QR pour les applications d’authentification dans ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) pour ASP.NET Core 2.0 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="a04ad-106">See [Enabling QR Code generation for authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="a04ad-107">Ce didacticiel montre comment configurer l’authentification à deux facteurs (2FA) à l’aide de SMS.</span><span class="sxs-lookup"><span data-stu-id="a04ad-107">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="a04ad-108">Des instructions sont fournies pour [twilio](https://www.twilio.com/) et [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), mais vous pouvez utiliser n’importe quel autre fournisseur SMS.</span><span class="sxs-lookup"><span data-stu-id="a04ad-108">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="a04ad-109">Nous vous recommandons de terminer [Confirmation du compte et la récupération de mot de passe](accconfirm.md) avant de commencer ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="a04ad-109">We recommend you complete [Account Confirmation and Password Recovery](accconfirm.md) before starting this tutorial.</span></span>

<span data-ttu-id="a04ad-110">Afficher le [exemple terminé](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span><span class="sxs-lookup"><span data-stu-id="a04ad-110">View the [completed sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="a04ad-111">[Comment télécharger](xref:tutorials/index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="a04ad-111">[How to download](xref:tutorials/index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="a04ad-112">Créez un nouveau projet ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a04ad-112">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="a04ad-113">Créer une application web ASP.NET Core nommée `Web2FA` avec les comptes d’utilisateur individuels.</span><span class="sxs-lookup"><span data-stu-id="a04ad-113">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="a04ad-114">Suivez les instructions de [SSL de l’application dans une application ASP.NET Core](xref:security/enforcing-ssl) pour configurer et exiger le protocole SSL.</span><span class="sxs-lookup"><span data-stu-id="a04ad-114">Follow the instructions in [Enforcing SSL in an ASP.NET Core app](xref:security/enforcing-ssl) to set up and require SSL.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="a04ad-115">Créer un compte SMS</span><span class="sxs-lookup"><span data-stu-id="a04ad-115">Create an SMS account</span></span>

<span data-ttu-id="a04ad-116">Créer un compte SMS, par exemple, à partir de [twilio](https://www.twilio.com/) ou [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span><span class="sxs-lookup"><span data-stu-id="a04ad-116">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="a04ad-117">Enregistrer les informations d’identification d’authentification (pour twilio : accountSid et du jeton d’authentification, pour ASPSMS : clé d’utilisateur a et le mot de passe).</span><span class="sxs-lookup"><span data-stu-id="a04ad-117">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="a04ad-118">Déterminer les informations d’identification du fournisseur SMS</span><span class="sxs-lookup"><span data-stu-id="a04ad-118">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="a04ad-119">**Twilio :**</span><span class="sxs-lookup"><span data-stu-id="a04ad-119">**Twilio:**</span></span>  
<span data-ttu-id="a04ad-120">Sous l’onglet tableau de bord de votre compte Twilio, copiez la **SID de compte** et **Auth jeton**.</span><span class="sxs-lookup"><span data-stu-id="a04ad-120">From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token**.</span></span>

<span data-ttu-id="a04ad-121">**ASPSMS :**</span><span class="sxs-lookup"><span data-stu-id="a04ad-121">**ASPSMS:**</span></span>  
<span data-ttu-id="a04ad-122">À partir des paramètres de votre compte, accédez à **clé d’utilisateur a** et copiez-le avec votre **mot de passe**.</span><span class="sxs-lookup"><span data-stu-id="a04ad-122">From your account settings, navigate to **Userkey** and copy it together with your **Password**.</span></span>

<span data-ttu-id="a04ad-123">Nous allons stocker ultérieurement ces valeurs avec l’outil Gestionnaire de secret dans les clés de `SMSAccountIdentification` et `SMSAccountPassword`.</span><span class="sxs-lookup"><span data-stu-id="a04ad-123">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="a04ad-124">Spécifiant l’ID d’expéditeur / donneur d’ordre</span><span class="sxs-lookup"><span data-stu-id="a04ad-124">Specifying SenderID / Originator</span></span>

<span data-ttu-id="a04ad-125">**Twilio :**</span><span class="sxs-lookup"><span data-stu-id="a04ad-125">**Twilio:**</span></span>  
<span data-ttu-id="a04ad-126">À partir de l’onglet numéros, copiez votre Twilio **numéro de téléphone**.</span><span class="sxs-lookup"><span data-stu-id="a04ad-126">From the Numbers tab, copy your Twilio **phone number**.</span></span> 

<span data-ttu-id="a04ad-127">**ASPSMS :**</span><span class="sxs-lookup"><span data-stu-id="a04ad-127">**ASPSMS:**</span></span>  
<span data-ttu-id="a04ad-128">Dans le Menu des expéditeurs déverrouiller, déverrouillage d’un ou plusieurs expéditeurs, ou choisissez un donneur d’ordre alphanumérique (non pris en charge par tous les réseaux).</span><span class="sxs-lookup"><span data-stu-id="a04ad-128">Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span> 

<span data-ttu-id="a04ad-129">Nous allons stocker plus tard cette valeur avec l’outil Gestionnaire de la clé secrète au sein de la clé `SMSAccountFrom`.</span><span class="sxs-lookup"><span data-stu-id="a04ad-129">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>


### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="a04ad-130">Fournissez les informations d’identification pour le service SMS</span><span class="sxs-lookup"><span data-stu-id="a04ad-130">Provide credentials for the SMS service</span></span>

<span data-ttu-id="a04ad-131">Nous allons utiliser la [modèle d’Options](xref:fundamentals/configuration/options) pour accéder aux paramètres de compte et une clé utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a04ad-131">We'll use the [Options pattern](xref:fundamentals/configuration/options) to access the user account and key settings.</span></span> 

   * <span data-ttu-id="a04ad-132">Créez une classe pour extraire la clé SMS sécurisée.</span><span class="sxs-lookup"><span data-stu-id="a04ad-132">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="a04ad-133">Pour cet exemple, le `SMSoptions` classe est créée dans le *Services/SMSoptions.cs* fichier.</span><span class="sxs-lookup"><span data-stu-id="a04ad-133">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

[!code-csharp[Main](2fa/sample/Web2FA/Services/SMSoptions.cs)]

<span data-ttu-id="a04ad-134">Définir le `SMSAccountIdentification`, `SMSAccountPassword` et `SMSAccountFrom` avec la [outil Gestionnaire de secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="a04ad-134">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="a04ad-135">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a04ad-135">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```
* <span data-ttu-id="a04ad-136">Ajoutez le package NuGet pour le fournisseur SMS.</span><span class="sxs-lookup"><span data-stu-id="a04ad-136">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="a04ad-137">À partir du Package Manager Console (PMC) exécuter :</span><span class="sxs-lookup"><span data-stu-id="a04ad-137">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="a04ad-138">**Twilio :**</span><span class="sxs-lookup"><span data-stu-id="a04ad-138">**Twilio:**</span></span>  
`Install-Package Twilio`

<span data-ttu-id="a04ad-139">**ASPSMS :**</span><span class="sxs-lookup"><span data-stu-id="a04ad-139">**ASPSMS:**</span></span>  
`Install-Package ASPSMS`


* <span data-ttu-id="a04ad-140">Ajoutez le code dans le *Services/MessageServices.cs* fichier pour activer les SMS.</span><span class="sxs-lookup"><span data-stu-id="a04ad-140">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="a04ad-141">Utilisez la Twilio ou la section ASPSMS :</span><span class="sxs-lookup"><span data-stu-id="a04ad-141">Use either the Twilio or the ASPSMS section:</span></span>


<span data-ttu-id="a04ad-142">**Twilio :**</span><span class="sxs-lookup"><span data-stu-id="a04ad-142">**Twilio:**</span></span>  
[!code-csharp[Main](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

<span data-ttu-id="a04ad-143">**ASPSMS :**</span><span class="sxs-lookup"><span data-stu-id="a04ad-143">**ASPSMS:**</span></span>  
[!code-csharp[Main](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="a04ad-144">Configurer le démarrage à utiliser`SMSoptions`</span><span class="sxs-lookup"><span data-stu-id="a04ad-144">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="a04ad-145">Ajouter `SMSoptions` au conteneur de service dans le `ConfigureServices` méthode dans le *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a04ad-145">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs*:</span></span>

[!code-csharp[Main](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="a04ad-146">Activer l’authentification à deux facteurs</span><span class="sxs-lookup"><span data-stu-id="a04ad-146">Enable two-factor authentication</span></span>

<span data-ttu-id="a04ad-147">Ouvrez le *Views/Manage/Index.cshtml* fichier de vue Razor et supprimer les caractères de commentaire (aucune balise n’est donc commenté).</span><span class="sxs-lookup"><span data-stu-id="a04ad-147">Open the *Views/Manage/Index.cshtml* Razor view file and remove the comment characters (so no markup is commnted out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="a04ad-148">Se connecter avec l’authentification à deux facteurs</span><span class="sxs-lookup"><span data-stu-id="a04ad-148">Log in with two-factor authentication</span></span>

* <span data-ttu-id="a04ad-149">Exécutez l’application et inscrire un nouvel utilisateur</span><span class="sxs-lookup"><span data-stu-id="a04ad-149">Run the app and register a new user</span></span>

![Affichage de Registre Web application ouverte dans Microsoft Edge](2fa/_static/login2fa1.png)

* <span data-ttu-id="a04ad-151">Cliquez sur votre nom d’utilisateur, ce qui active la `Index` méthode d’action de contrôleur de gestion.</span><span class="sxs-lookup"><span data-stu-id="a04ad-151">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="a04ad-152">Puis cliquez sur le numéro de téléphone **ajouter** lien.</span><span class="sxs-lookup"><span data-stu-id="a04ad-152">Then tap the phone number **Add** link.</span></span>

![Gérer les affichages](2fa/_static/login2fa2.png)

* <span data-ttu-id="a04ad-154">Ajouter un numéro de téléphone qui recevra le code de vérification et appuyez sur **envoyer le code de vérification**.</span><span class="sxs-lookup"><span data-stu-id="a04ad-154">Add a phone number that will receive the verification code, and tap **Send verification code**.</span></span>

![Ajouter une page de numéro de téléphone](2fa/_static/login2fa3.png)

* <span data-ttu-id="a04ad-156">Vous obtenez un message texte avec le code de vérification.</span><span class="sxs-lookup"><span data-stu-id="a04ad-156">You will get a text message with the verification code.</span></span> <span data-ttu-id="a04ad-157">Entrer et appuyez sur **envoyer**</span><span class="sxs-lookup"><span data-stu-id="a04ad-157">Enter it and tap **Submit**</span></span>

![Vérifier la page du numéro de téléphone](2fa/_static/login2fa4.png)

<span data-ttu-id="a04ad-159">Si vous n’obtenez pas un message texte, consultez la page du journal twilio.</span><span class="sxs-lookup"><span data-stu-id="a04ad-159">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="a04ad-160">L’affichage de gestion affiche que votre numéro de téléphone a été ajouté avec succès.</span><span class="sxs-lookup"><span data-stu-id="a04ad-160">The Manage view shows your phone number was added successfully.</span></span>

![Gérer les affichages](2fa/_static/login2fa5.png)

* <span data-ttu-id="a04ad-162">Appuyez sur **activer** pour activer l’authentification à deux facteurs.</span><span class="sxs-lookup"><span data-stu-id="a04ad-162">Tap **Enable** to enable two-factor authentication.</span></span>

![Gérer les affichages](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="a04ad-164">Authentification à deux facteurs test</span><span class="sxs-lookup"><span data-stu-id="a04ad-164">Test two-factor authentication</span></span>

* <span data-ttu-id="a04ad-165">Fermez la session.</span><span class="sxs-lookup"><span data-stu-id="a04ad-165">Log off.</span></span>

* <span data-ttu-id="a04ad-166">Se connecter.</span><span class="sxs-lookup"><span data-stu-id="a04ad-166">Log in.</span></span>

* <span data-ttu-id="a04ad-167">Le compte d’utilisateur a activé l’authentification à deux facteurs, vous devez fournir le second facteur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="a04ad-167">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="a04ad-168">Dans ce didacticiel vous avez activé la vérification par téléphone.</span><span class="sxs-lookup"><span data-stu-id="a04ad-168">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="a04ad-169">Les modèles intégrés vous permettent également à configurer la messagerie en tant que le second facteur.</span><span class="sxs-lookup"><span data-stu-id="a04ad-169">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="a04ad-170">Vous pouvez configurer des facteurs de deuxième supplémentaires pour l’authentification telles que des codes QR.</span><span class="sxs-lookup"><span data-stu-id="a04ad-170">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="a04ad-171">Appuyez sur **envoyer**.</span><span class="sxs-lookup"><span data-stu-id="a04ad-171">Tap **Submit**.</span></span>

![Affichage du Code de vérification d’envoi](2fa/_static/login2fa7.png)

* <span data-ttu-id="a04ad-173">Entrez le code que vous obtenez dans le message SMS.</span><span class="sxs-lookup"><span data-stu-id="a04ad-173">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="a04ad-174">En cliquant sur le **n’oubliez pas de ce navigateur** case à cocher vous exempter de devoir utiliser 2FA pour vous connecter lorsque vous utilisez le même périphérique et le navigateur.</span><span class="sxs-lookup"><span data-stu-id="a04ad-174">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="a04ad-175">L’activation de 2FA et en cliquant sur **n’oubliez pas de ce navigateur** vous fournira fort 2FA protection contre les utilisateurs malveillants, essayez d’accéder à votre compte, tant qu’ils n’ont pas accès à votre appareil.</span><span class="sxs-lookup"><span data-stu-id="a04ad-175">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="a04ad-176">Pour cela, sur n’importe quel appareil privé que vous utilisez régulièrement.</span><span class="sxs-lookup"><span data-stu-id="a04ad-176">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="a04ad-177">En définissant **n’oubliez pas de ce navigateur**, vous obtenez la sécurité renforcée de 2FA à partir d’appareils que vous n’utilisez pas régulièrement, et la commodité sur ne pas avoir à passer par 2FA sur vos propres appareils.</span><span class="sxs-lookup"><span data-stu-id="a04ad-177">By setting  **Remember this browser**, you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![Vérifier l’affichage](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="a04ad-179">Verrouillage de compte pour la protection contre les attaques en force brute</span><span class="sxs-lookup"><span data-stu-id="a04ad-179">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="a04ad-180">Nous vous recommandons de qu'utiliser le verrouillage de compte avec 2FA.</span><span class="sxs-lookup"><span data-stu-id="a04ad-180">We recommend you use account lockout with 2FA.</span></span> <span data-ttu-id="a04ad-181">Une fois qu’un utilisateur se connecte (via un compte local ou social), chaque tentative ayant échoué 2FA est stockée et si le nombre maximal de tentatives (valeur par défaut est 5) est atteinte, l’utilisateur est verrouillé pendant cinq minutes (vous pouvez définir l’heure avec verrouillage `DefaultAccountLockoutTimeSpan`).</span><span class="sxs-lookup"><span data-stu-id="a04ad-181">Once a user logs in (through a local account or social account), each failed attempt at 2FA is stored, and if the maximum attempts (default is 5) is reached, the user is locked out for five minutes (you can set the lock out time with `DefaultAccountLockoutTimeSpan`).</span></span> <span data-ttu-id="a04ad-182">Les éléments suivants configurent compte peuvent être verrouillés pendant 10 minutes après 10 tentatives ayant échoué.</span><span class="sxs-lookup"><span data-stu-id="a04ad-182">The following configures Account to be locked out for 10 minutes after 10 failed attempts.</span></span>

[!code-csharp[Main](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)] 