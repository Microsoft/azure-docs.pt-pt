---
title: Preparação do pacote AppSource | Azure Marketplace
description: Explanaion em como preparar e criar pacotes AppSource.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 2c6b78e62afb43562910c872d31e2c9f564040da
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73806103"
---
# <a name="appsource-package-preparation"></a>Preparação do pacote AppSource

Além de um arquivo. zip de solução, você precisará de um **pacote AppSource**. Esse é um arquivo. zip que inclui todos os ativos necessários para automatizar o processo de implantação de sua solução no ambiente de CRM dos clientes. O **pacote AppSource** tem os seguintes componentes

* Pacote para o implantador de pacote
* Arquivo **Content_Types. xml** com os ativos que você usa
* arquivo XML com seus dados específicos do aplicativo
* logotipo de 32x32 que será exibido com sua listagem no centro de administração
* Termos de licença, política de privacidade

As etapas a seguir ajudarão você a criar seu pacote AppSource.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Criar um pacote para o implantador de pacote

O pacote para o implantador de pacote é uma parte do pacote AppSource.

Para criar um pacote para o implantador de pacote, use as seguintes instruções: [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx). Ao concluir, o pacote consistirá nos ativos abaixo:

1. Pasta do pacote: contém todas as soluções, dados de configuração, arquivos simples e o conteúdo do pacote. _Observação: no exemplo a seguir, vamos supor que a pasta do pacote é chamada de "PkgFolder"_
2. dll: o assembly contém o código personalizado para seu pacote. _Observação: no exemplo a seguir, vamos supor que esse arquivo é chamado de "MicrosoftSample. dll"._

Agora, você precisa criar um arquivo chamado "**Content_Types. xml**". esse arquivo listará todas as extensões de ativos que fazem parte do seu pacote. Aqui está um código de exemplo para o arquivo.

    <?xml version="1.0" encoding="utf-8"?>
        <Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">
        <Default Extension="xml" ContentType="application/octet-stream" />
        <Default Extension="xaml" ContentType="application/octet-stream" />
        <Default Extension="dll" ContentType="application/octet-stream" />
        <Default Extension="zip" ContentType="application/octet-stream" />
        <Default Extension="jpg" ContentType="application/octet-stream" />
        <Default Extension="gif" ContentType="application/octet-stream" />
        <Default Extension="png" ContentType="application/octet-stream" />
        <Default Extension="htm" ContentType="application/octet-stream" />
        <Default Extension="html" ContentType="application/octet-stream" />
        <Default Extension="db" ContentType="application/octet-stream" />
        <Default Extension="css" ContentType="application/octet-stream" />
    </Types>

A etapa final é compactar o seguinte em um arquivo. Chame-o **Package. zip**. Ele conterá

1. PkgFolder (incluindo tudo dentro da pasta)
2. dll
3. **Content_Types. xml**

Etapas para criar Package. zip:

1. Coloque a pasta do pacote, o arquivo **Content_Types. xml** e o PackageName. dll em um diretório.

![CRMScreenShot2](media/CRMScreenShot2.png)

1. Selecione todos os itens na pasta, clique com o botão direito do mouse e escolha enviar para pasta compactada (zip)

![CRMScreenShot3](media/CRMScreenShot3.png)

1. Altere o nome para Package. zip

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Criar um pacote AppSource

O pacote AppSource requer alguns arquivos adicionais.

1. jpg (resolução de 32x32)
2. HTML (arquivo HTML formatado)
3. **Content_Types. xml** (o mesmo que acima)
4. xml

Aqui está um código de exemplo para Input. xml. Consulte definições na tabela abaixo.

    <PvsPackageData>
        <ProviderName>Microsoft</ProviderName>
        <PackageFile>package.zip</PackageFile>
        <SolutionAnchorName>SampleSolution.zip</SolutionAnchorName>
        <StartDate>01/01/2016</StartDate>
        <EndDate>01/01/2021</EndDate>
        <SupportedCountries>US,CA</SupportedCountries>
        <LearnMoreLink>https://www.microsoft.com</LearnMoreLink>
        <Locales>
        <PackageLocale Code="1033" IsDefault="true">
        <Logo>logo32x32.png</Logo>
        <Terms>
        <PackageTerm File="TermsOfUse.html" />
        </Terms>
        </PackageLocale>
        </Locales>
    </PvsPackageData>
 
**Posição**

|Campo|Detalhes|
|---|---|
|ProviderName|De quem é a solução que vem. Se for uma equipe da Microsoft, deve ser a Microsoft.|
|PackageFile |Ativos do implantador de pacote são compactados junto com um arquivo Content\_Types. xml. Esse arquivo zip deve conter o assembly do implantador de pacote e a pasta com os ativos do implantador de pacote. Ou seja, Package. zip|
|SolutionAnchorName |Nome do arquivo zip da solução no implantador de pacote que é usado para o nome de exibição e a descrição dos ativos da solução.|
| Início| Essa é a data em que o pacote de solução ficará disponível. O formato é MM/DD/AAAA|
|Término|Essa é a data em que o pacote de solução deixará de ser disponibilizado. O formato é MM/DD/AAAA |
|SupportedCountries |Esta é uma lista delimitada por vírgulas de países/regiões que devem ver este pacote. Contate a serviços online para obter uma lista de todos os códigos de país atuais. No momento, isso está escrevendo a lista: AE, AL, AM, ao, AR, AT, AU, AZ, BA, BB, BD, ser, BG, BH, BM, bilhão, BO, BR, BY, CA, CH, CI, CL, CM, CO, CR, CV, PV, CY, CZ, DE, DK, DO, DZ, EC, vc, por exemplo , ES, FI, FR, GB, GE, GH, GR, GT, HK, HN, HR, HU, ID, IE, IL, IN, IQ, IS, IT, JM, JO, JP, KE, KG, KN, KR, KW, KY, KZ, LB, LK, LT, LU, LV, LY, MA, MC, MD, ME, MK, MN, MO, MT, MU, MX, O MEU , NG, NI, NL, NÃO, NZ, OM, PA, PE, PH, CP, PL, PR, PS, PT, PY, QA, RO, RS, RU, RW, SA, SE, SG, SI, SK, SN, VA, TH, TM, TN, TR, TT, TW, UA, US, UY, UZ, VE, VI, VN, ZA, ZW |
|LearnMoreLink | URL para a página mais informações deste pacote. |
|Localidades|Uma instância deste nó para cada linguagem UX que você deseja dar suporte no UX de solução preferencial. Este nó contém filhos que descrevem a localidade, o logotipo e os termos de cada idioma|
|Localidades: PackageLocale. Code|LCID do idioma para este nó. Exemplo: inglês americano é 1033|
|Localidades: PackageLocale. IsDefault|Indica que esse é o idioma padrão. Isso será usado como o idioma de fallback se a linguagem UX escolhida pelo cliente não estiver disponível.|
|Localidades: logotipo|Isso se o logotipo que você deseja usar para este pacote. O tamanho do ícone é 32x32. Os formatos permitidos são PNG e JPG|
|Localidades: termos: PackageTerm. File|Esse é o nome de arquivo do documento HTML que contém os termos de licença.|

Aqui está o local onde o logotipo será exibido:

![CRMScreenShot5](media/CRMScreenShot5.png)

A etapa final é compactar o seguinte em um arquivo.

1. zip (criado anteriormente)
2. **Content_Types. xml**
3. xml
4. png
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

Renomeie o arquivo para algo apropriado para seu aplicativo. Preferimos que você inclua o nome da sua empresa e o nome do aplicativo. Por exemplo: **_Microsoft_SamplePackage. zip**.
