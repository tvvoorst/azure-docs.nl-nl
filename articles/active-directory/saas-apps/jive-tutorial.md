---
title: 'Zelfstudie: Azure Active Directory-integratie met Jive | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Jive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9fc5659a-c116-4a1b-a601-333325a26b46
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.openlocfilehash: cebcfb4614d1f685697bed6914f80237e175fb7b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436553"
---
# <a name="tutorial-azure-active-directory-integration-with-jive"></a>Zelfstudie: Azure Active Directory-integratie met Jive

In deze zelfstudie leert u hoe u Jive integreren met Azure Active Directory (Azure AD).

Jive integreren met Azure AD biedt u de volgende voordelen:

- U kunt beheren in Azure AD die toegang tot Jive heeft
- U kunt uw gebruikers automatisch ophalen aangemeld bij Jive (Single Sign-On) met hun Azure AD-accounts inschakelen
- U kunt uw accounts in één centrale locatie - Azure portal beheren

Als u meer informatie over SaaS-app-integratie met Azure AD wilt, Zie [wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Jive, moet u de volgende items:

- Een Azure AD-abonnement
- Een Jive eenmalige aanmelding ingeschakeld abonnement

> [!NOTE]
> Als u wilt testen van de stappen in deze zelfstudie, raden we niet met behulp van een productie-omgeving.

Als u wilt testen van de stappen in deze zelfstudie, moet u deze aanbevelingen volgen:

- Gebruik uw productie-omgeving, niet als dat nodig is.
- Als u geen een proefversie Azure AD-omgeving hebt, krijgt u een proefversie van één maand [hier](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeschrijving
In deze zelfstudie test u de Azure AD eenmalige aanmelding in een testomgeving.
Het scenario in deze zelfstudie bestaat uit twee belangrijkste bouwstenen:

1. Toevoegen van Jive uit de galerie
1. Configureren en testen van Azure AD eenmalige aanmelding

## <a name="adding-jive-from-the-gallery"></a>Toevoegen van Jive uit de galerie
Voor het configureren van de integratie van Jive in Azure AD, moet u Jive uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u wilt toevoegen Jive uit de galerie, moet u de volgende stappen uitvoeren:**

1. In de  **[Azure-portal](https://portal.azure.com)**, klik in het navigatievenster aan de linkerkant op **Azure Active Directory** pictogram. 

    ![Active Directory][1]

1. Navigeer naar **bedrijfstoepassingen**. Ga vervolgens naar **alle toepassingen**.

    ![Toepassingen][2]

1. Nieuwe toepassing toevoegen, klikt u op **nieuwe toepassing** knop boven aan het dialoogvenster.

    ![Toepassingen][3]

1. Typ in het zoekvak **Jive**.

    ![Het maken van een Azure AD-testgebruiker](./media/jive-tutorial/tutorial_jive_search.png)

1. Selecteer in het deelvenster resultaten **Jive**, en klik vervolgens op **toevoegen** om toe te voegen van de toepassing.

    ![Het maken van een Azure AD-testgebruiker](./media/jive-tutorial/tutorial_jive_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configureren en testen van Azure AD eenmalige aanmelding
In deze sectie kunt u configureren en testen Azure AD eenmalige aanmelding met Jive op basis van een testgebruiker met de naam "Britta Simon."

Voor eenmalige aanmelding om te werken, moet Azure AD om te weten wat de gebruiker equivalent in Jive is aan een gebruiker in Azure AD. Met andere woorden, moet een koppeling relatie tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Jive tot stand worden gebracht.

Deze relatie koppeling tot stand is gebracht door toe te wijzen de waarde van de **gebruikersnaam** in Azure AD als de waarde van de **gebruikersnaam** in Jive.

Om te configureren en testen van Azure AD eenmalige aanmelding met Jive, moet u de volgende bouwstenen voltooien:

1. **[Configureren van Azure AD eenmalige aanmelding](#configuring-azure-ad-single-sign-on)**  : als u wilt dat uw gebruikers kunnen deze functie gebruiken.
1. **[Het maken van een Azure AD-testgebruiker](#creating-an-azure-ad-test-user)**  - voor het testen van Azure AD eenmalige aanmelding met Britta Simon.
1. **[Het maken van een testgebruiker Jive](#creating-a-jive-test-user)**  : als u wilt een equivalent van Britta Simon in Jive die is gekoppeld aan de Azure AD-weergave van de gebruiker hebben.
1. **[Toewijzen van de Azure AD-testgebruiker](#assigning-the-azure-ad-test-user)**  - Britta Simon gebruik van Azure AD eenmalige aanmelding inschakelen.
1. **[Eenmalige aanmelding testen](#testing-single-sign-on)**  : als u wilt controleren of de configuratie werkt.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD eenmalige aanmelding configureren

In deze sectie maakt u schakelt Azure AD eenmalige aanmelding in de Azure-portal en configureren van eenmalige aanmelding in uw toepassing Jive.

**Voor het configureren van Azure AD eenmalige aanmelding met Jive, moet u de volgende stappen uitvoeren:**

1. In de Azure-portal op de **Jive** toepassingspagina integratie, klikt u op **eenmalige aanmelding**.

    ![Eenmalige aanmelding configureren][4]

1. Op de **eenmalige aanmelding** dialoogvenster, selecteer **modus** als **SAML gebaseerde aanmelding** eenmalige aanmelding inschakelen.

    ![Eenmalige aanmelding configureren](./media/jive-tutorial/tutorial_jive_samlbase.png)

1. Op de **Jive domein en URL's** sectie, voert u de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/jive-tutorial/tutorial_jive_url.png)

    a. In de **aanmeldings-URL** tekstvak, een URL met behulp van het volgende patroon: `https://<instance name>.jivecustom.com`

    b. In de **id** tekstvak, een URL met behulp van het volgende patroon: `https://<instance name>.jiveon.com`

    > [!NOTE]
    > Deze waarden zijn niet de werkelijke. Werk deze waarden met de werkelijke aanmeldings-URL en -id. Neem contact op met [Jive Client ondersteuningsteam](https://www.jivesoftware.com/services-support/) om deze waarden te verkrijgen.

1. Op de **SAML-handtekeningcertificaat** sectie, klikt u op **Metadata XML** en sla het XML-bestand op uw computer.

    ![Eenmalige aanmelding configureren](./media/jive-tutorial/tutorial_jive_certificate.png)

1. Klik op **opslaan** knop.

    ![Eenmalige aanmelding configureren](./media/jive-tutorial/tutorial_general_400.png)

1. Het configureren van eenmalige aanmelding op **Jive** aan clientzijde, aanmelding bij uw tenant Jive als beheerder.

1. In het menu aan de bovenkant, klikt u op '**Saml**. "

    ![Single Sign-On aan App configureren](./media/jive-tutorial/tutorial_jive_002.png)

    a. Selecteer **ingeschakeld** onder de **algemene** tabblad b. Klik op de '**alle saml-instellingen opslaan**"knop.

1. Navigeer naar de "**Idp metagegevens**" tabblad.

    ![Single Sign-On aan App configureren](./media/jive-tutorial/tutorial_jive_003.png)

    a. Kopieer de inhoud van het gedownloade metagegevens-XML-bestand en plak deze in de **Identity Provider (IDP) metagegevens** tekstvak.

    b. Klik op de '**alle saml-instellingen opslaan**"knop.

1. Ga naar het '**kenmerk Gebruikerstoewijzing**"tabblad.

    ![Single Sign-On aan App configureren](./media/jive-tutorial/tutorial_jive_004.png)

    a. In de **e** tekstvak, kopieert en plakt u de naam van het kenmerk van **e-mail** waarde.

    b. In de **voornaam** tekstvak, kopieert en plakt u de naam van het kenmerk van **givenname** waarde.

    c. In de **achternaam** tekstvak, kopieert en plakt u de naam van het kenmerk van **achternaam** waarde.

### <a name="creating-an-azure-ad-test-user"></a>Het maken van een Azure AD-testgebruiker
Het doel van deze sectie is het maken van een testgebruiker in Azure portal Britta Simon genoemd.

![Azure AD-gebruiker maken][100]

**Als u wilt een testgebruiker maken in Azure AD, moet u de volgende stappen uitvoeren:**

1. In de **Azure-portal**, klik op het navigatiedeelvenster links **Azure Active Directory** pictogram.

    ![Het maken van een Azure AD-testgebruiker](./media/jive-tutorial/create_aaduser_01.png) 

1. Als u wilt weergeven in de lijst met gebruikers, gaat u naar **gebruikers en groepen** en klikt u op **alle gebruikers**.

    ![Het maken van een Azure AD-testgebruiker](./media/jive-tutorial/create_aaduser_02.png)

1. Om te openen de **gebruiker** dialoogvenster, klikt u op **toevoegen** boven aan het dialoogvenster.

    ![Het maken van een Azure AD-testgebruiker](./media/jive-tutorial/create_aaduser_03.png) 

1. Op de **gebruiker** dialoogvenster pagina, voert u de volgende stappen uit:

    ![Het maken van een Azure AD-testgebruiker](./media/jive-tutorial/create_aaduser_04.png)

    a. In de **naam** tekstvak, type **BrittaSimon**.

    b. In de **gebruikersnaam** tekstvak, type de **e-mailadres** van BrittaSimon.

    c. Selecteer **wachtwoord weergeven** en noteer de waarde van de **wachtwoord**.

    d. Klik op **Create**.

### <a name="creating-a-jive-test-user"></a>Het maken van een testgebruiker Jive

Het doel van deze sectie is het maken van een gebruiker met de naam van Britta Simon in Jive. Jive ondersteunt automatisch gebruikers inrichten, dit is standaard ingeschakeld. Meer informatie vindt u [hier](jive-provisioning-tutorial.md) voor het automatisch inrichten van gebruikers configureren.

Als u moet de gebruiker handmatig hebt gemaakt, samen met [Jive Client ondersteuningsteam](https://www.jivesoftware.com/services-support/) om toe te voegen de gebruikers in het Jive-platform.

### <a name="assigning-the-azure-ad-test-user"></a>Toewijzen aan de gebruiker van de test Azure AD

In deze sectie maakt inschakelen u Britta Simon gebruiken Azure eenmalige aanmelding door toegang te verlenen aan Jive.

![Gebruiker toewijzen][200] 

**Als u wilt Britta Simon aan Jive toewijst, moet u de volgende stappen uitvoeren:**

1. Open de weergave toepassingen in de Azure-portal en gaat u naar de mapweergave en Ga naar **bedrijfstoepassingen** klikt u vervolgens op **alle toepassingen**.

    ![Gebruiker toewijzen][201] 

1. Selecteer in de lijst met toepassingen, **Jive**.

    ![Eenmalige aanmelding configureren](./media/jive-tutorial/tutorial_jive_app.png) 

1. Klik in het menu aan de linkerkant op **gebruikers en groepen**.

    ![Gebruiker toewijzen][202] 

1. Klik op **toevoegen** knop. Selecteer vervolgens **gebruikers en groepen** op **toevoegen toewijzing** dialoogvenster.

    ![Gebruiker toewijzen][203]

1. Op **gebruikers en groepen** dialoogvenster, selecteer **Britta Simon** in de lijst gebruikers.

1. Klik op **Selecteer** op knop **gebruikers en groepen** dialoogvenster.

1. Klik op **toewijzen** op knop **toevoegen toewijzing** dialoogvenster.
    
### <a name="testing-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie maakt testen u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster.

Wanneer u op de tegel Jive in het toegangsvenster, u moet u automatisch aangemeld bij uw toepassing Jive.

## <a name="additional-resources"></a>Aanvullende resources

* [Lijst met zelfstudies over het integreren van SaaS-Apps met Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [Inrichten van gebruikers configureren](jive-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/jive-tutorial/tutorial_general_01.png
[2]: ./media/jive-tutorial/tutorial_general_02.png
[3]: ./media/jive-tutorial/tutorial_general_03.png
[4]: ./media/jive-tutorial/tutorial_general_04.png

[100]: ./media/jive-tutorial/tutorial_general_100.png

[200]: ./media/jive-tutorial/tutorial_general_200.png
[201]: ./media/jive-tutorial/tutorial_general_201.png
[202]: ./media/jive-tutorial/tutorial_general_202.png
[203]: ./media/jive-tutorial/tutorial_general_203.png
