---
title: Preparação do pacote do AppSource | Documentos da Microsoft
description: Explanaion em como preparar e criar pacotes do AppSource.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: cc06ddf2fce93bef27e7aaee85b47179608e192b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837040"
---
# <a name="appsource-package-preparation"></a>Preparação do pacote do AppSource

Para além de um arquivo de solution.zip, terá uma **AppSource pacote**. Este é um ficheiro. zip que inclui todos os recursos necessários para automatizar o processo de implementar a sua solução no ambiente de CRM dos clientes. O **AppSource pacote** tem os seguintes componentes

* Pacote para o Package Deployer
* **Content_Types** ficheiro com os recursos que utilizar
* ficheiro XML com os seus dados específicos da aplicação
* logótipo de 32 x 32 que será apresentado com a sua listagem no Centro de administração
* Termos de licença, política de privacidade

Os passos abaixo irão ajudá-lo a criar seu pacote do AppSource.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Criar um pacote para o Package Deployer

O pacote para o Package Deployer é uma parte do pacote do AppSource.

Para criar um pacote para o Package Deployer, utilize as seguintes instruções: [ https://msdn.microsoft.com/library/dn688182.aspx ](https://msdn.microsoft.com/library/dn688182.aspx). Quando terminar, o pacote consistirá os recursos abaixo:

1. Pasta do pacote: contém todas as soluções, dados de configuração, ficheiros simples e o conteúdo para o seu pacote. _Nota: No exemplo a seguir vamos supor que a pasta do pacote é chamada "PkgFolder"_
2. DLL: O assembly contém o código personalizado para o seu pacote. _Nota: No exemplo a seguir vamos supor que esse arquivo é chamado "MicrosoftSample.dll."_

Agora, tem de criar um arquivo chamado "**Content_Types**" este ficheiro irá listar todas as extensões de recursos que fazem parte do pacote. Aqui está o código de exemplo para o ficheiro.

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

A etapa final é o seguinte num ficheiro de ZIP. Chamá-lo **Package**. Ele conterá

1. PkgFolder (incluindo tudo dentro da pasta)
2. DLL
3. **Content_Types.xml**

Passos para criar Package:

1. Colocar a pasta de pacote **Content_Types** ficheiro e PackageName.dll num diretório.

![CRMScreenShot2](media/CRMScreenShot2.png)

1. Selecione todos os itens na pasta, clique com botão direito e selecione Enviar para (zip) pasta comprimida

![CRMScreenShot3](media/CRMScreenShot3.png)

1. Altere o nome para o Package

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Criar um pacote do AppSource

O pacote de AppSource requer alguns arquivos adicionais.

1. JPG (resolução de 32 x 32)
2. HTML (arquivo formatado HTML)
3. **Content_Types** (tal como acima)
4. xml

Aqui está o código de exemplo para input.xml. Consulte as definições na tabela a seguir.

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
 
**Em que:**

|Campo|Detalhes|
|---|---|
|ProviderName|Quem é a solução provenientes. Se uma equipe da Microsoft, deve ser Microsoft.|
|PackageFile |Recursos do implementador comprimidos, juntamente com um conteúdo do pacote\_types.xml ficheiro. Este ficheiro zip deve conter o assembly de implementador de pacote e a pasta com os ativos de implementador de pacote. Ou seja, Package|
|SolutionAnchorName |Nome do ficheiro zip solução no package deployer que é utilizado para o nome a apresentar e a descrição de ativos de solução.|
| startDate| Esta é a data em que o pacote de solução irá tornar-se disponível. O formato é MM/DD/AAAA|
|endDate|Esta é a data em que o pacote de solução deixará de estar disponível. O formato é MM/DD/AAAA |
|SupportedCountries |Esta é uma lista delimitada por vírgulas de países que deve ver este pacote. Entre em contato com os serviços online para obter uma lista de todos os códigos de país atual. O momento da redação deste artigo lista foi: AE, AL, AM, PEDIDOS, AR, AT, AU, AZ, BA, BB, DB, SER, BG, BH, BM, BN, BO, BRASIL, PELA AC, CH, CI, CL, CM, CO, CR, CV, CW, CY, CZ, DE, DK, FAÇA, DZ, EC, EE, POR EXEMPLO, ES, FI, FR, GB, GE, GH, GR, GT, HK , HN, RH, HU, ID, IE, IL, IN, IQ, É, ELE, JM, JO, JP, KE, KG, KN, COREIA, KW, KY, KZ, LB, LK, LT, LU, LV, LY, MA, MC, MD, ME, MK, MN, MÊS, MT, MU, MX, MEU, NG, NI, NL, NÃO, NZ, OM, PA, PE, PH , PK, PL, PR, PS, HORA DO PACÍFICO, PY, CONTROLE DE QUALIDADE, RO, RS, RU, RW, SA, SE, SG, IS, SK, SN, SV, TH, TM, TN, TR, TT, TW, UA, E.U.A., UY, UZ, VE, VI, VN, ZA, ZW |
|LearnMoreLink | URL para a página de informações mais para este pacote. |
|Localidades|Uma instância deste nó para cada idioma de UX pretende oferecer apoio técnico na solução preferencial experiência do usuário. Este nó contém elementos subordinados que descrevem a Localidade, o logótipo e a termos para cada idioma|
|Localidades: PackageLocale.Code|LCID do idioma para este nó. Exemplo: Inglês dos Estados Unidos é 1033|
|Localidades: PackageLocale.IsDefault|Indica que este é o idioma predefinido. Isto é utilizado como Outono fazer uma cópia de linguagem se o idioma de UX escolhido pelo cliente não está disponível.|
|Localidades: Logótipo|Isso se o logótipo que pretende utilizar para este pacote. Tamanho de ícone é de 32 x 32. Os formatos permitidos são PNG e JPG|
|Localidades: termos: PackageTerm.File|Este é o nome de ficheiro de documentos HTML que contém os seus termos de licença.|

É aqui onde irá apresentar o logótipo:

![CRMScreenShot5](media/CRMScreenShot5.png)

A etapa final é o seguinte num ficheiro de ZIP.

1. ZIP (criado anteriormente)
2. **Content_Types.xml**
3. xml
4. PNG
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

Renomeie o arquivo para algo adequado para a sua aplicação. Preferimos que inclui o nome da empresa e o nome de aplicação. Por exemplo: **_Microsoft_SamplePackage.zip**.
