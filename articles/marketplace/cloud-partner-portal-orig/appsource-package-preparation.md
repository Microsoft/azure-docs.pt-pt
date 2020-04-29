---
title: Preparação do pacote appsource [ Mercado Azure
description: Explanações sobre como preparar e construir pacotes AppSource .
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 76f8cbd6cb16b585a7dbda7b2ffa5eeeeb1b68d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280614"
---
# <a name="appsource-package-preparation"></a>Preparação de pacotes appSource

Além de um ficheiro solution.zip, vai precisar de um **Pacote AppSource**. Trata-se de um ficheiro .zip que inclui todos os ativos necessários para automatizar o processo de implantação da sua solução para o ambiente CRM dos clientes. O **Pacote AppSource** tem os seguintes componentes

* Pacote para o desdobrador de pacotes
* **Content_Types.xml** ficheiro com os ativos que utiliza
* ficheiro xml com os seus dados específicos da App
* Logotipo 32x32 que irá exibir com a sua listagem no Centro de Administração
* Termos de licença, política de privacidade

Os passos abaixo irão ajudá-lo a criar o seu Pacote AppSource.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Criar um pacote para o desdobrador de pacotes

O pacote para o desdobrador de pacotes é uma parte do pacote AppSource.

Para criar um pacote para o desdobrador [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx)de pacotes, utilize as seguintes instruções: . Quando estiver concluído, o seu pacote será composto pelos ativos abaixo:

1. Pasta de pacote: contém todas as soluções, dados de configuração, ficheiros planos e o conteúdo do seu pacote. _Nota: No exemplo que se segue vamos assumir que a pasta do pacote se chama "PkgFolder"_
2. dll: O conjunto contém o código personalizado para o seu pacote. _Nota: No exemplo que se segue assumiremos que este ficheiro se chama "MicrosoftSample.dll"._

Agora, você precisa criar um ficheiro chamado "**Content_Types.xml**" Este ficheiro irá listar todas as extensões de ativos que fazem parte do seu pacote. Aqui está o código de exemplo para o ficheiro.

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

O passo final é fechar o seguinte num ficheiro. Chame-lhe **pacote.zip**. Vai conter

1. PkgFolder (incluindo tudo dentro da pasta)
2. dll
3. **Content_Types.xml**

Passos para criar pacote.zip:

1. Coloque a pasta do pacote, **ficheiro Content_Types.xml** e O PacoteNome.dll num só diretório.

![CRMScreenShot2](media/CRMScreenShot2.png)

1. Selecione todos os itens da pasta, clique à direita e escolha Enviar para comprimido (zip)

![CRMScreenShot3](media/CRMScreenShot3.png)

1. Mude o nome para pacote.zip

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Criar um pacote AppSource

O Pacote AppSource requer alguns ficheiros adicionais.

1. jpg (resolução 32x32)
2. html (ficheiro formado HTML)
3. **Content_Types.xml** (o mesmo que acima)
4. xml

Aqui está o código de exemplo para entrada.xml. Consulte as definições na tabela abaixo.

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
 
**Onde:**

|Campo|Detalhes|
|---|---|
|ProviderName|De quem é a solução que vem. Se uma equipa da Microsoft, esta deve ser a Microsoft.|
|PacoteFile |Pacote Deployer ativos zipped\_juntamente com um ficheiro tipos de conteúdo.xml. Este ficheiro zip deve conter o conjunto de implantação do pacote e a pasta com os ativos do desdobrador do pacote. Isto é, pacote.zip|
|Nome Âncora de Soluções |Nome do ficheiro zip da solução no desdobrador de pacotes que é utilizado para o nome de exibição e descrição dos ativos da solução.|
| StartDate| Esta é a data em que o pacote de soluções estará disponível. O formato é MM/DD/YYYY|
|Data final|Esta é a data em que o pacote de soluções deixará de estar disponível. O formato é MM/DD/YYYY |
|Países Apoiados |Trata-se de uma lista de países/regiões delimitada sem acordo que deverá ver este pacote. Contacte os serviços online para obter uma lista de todos os códigos do país em vigor. Na época, desta escrita a lista era: AE,AL,AM,AO,AR,AT,AU,AZ,BA,BB,BD,BE,BG,BH,BM,BN,BO,BR,BY,CA,CH,CI,CL,CM,CO,CR,CV,CW,CY,CZ,DE,DK,DO,DZ,EC,EE,EE,EG,ES,FI,FR,GB,GE,GH,GR,GT,HK,HN,HR,HU,ID,IE,IL ,IN,IQ,IS,IT,JM,JO,JP,KE,KG,KN,KR,KW,KY,KZ,LB,LK,LT,LU,LV,LY,MA,MC,MD,ME,MK,MN,MO,MT,MU,MX,MY,NG,NI,NL,NO,NZ,Om,PA,PE,PH,PK,PL,PR,PS,PT,PY,QA,RO,Rs,RU,RW,SA,SE,SG,SI,SK,SN,SV,TH,TM,TN ,TR,TT,TW,Ua,US,Uy,UZ,VI,VN,ZA,ZW |
|LearnMoreLink | URL para a página mais informações para este pacote. |
|Locais|Um exemplo deste nó para cada idioma UX que você quer suportar na solução preferida UX. Este nó contém crianças que descrevem o local, logotipo e termos para cada língua|
|Locais: PackageLocale.Code|LCID da língua para este nó. Exemplo: Us English é 1033|
|Locais: PackageLocale.IsDefault|Indica que esta é a língua padrão. Isto é usado como a linguagem de recuo se a Linguagem UX escolhida pelo cliente não estiver disponível.|
|Locais: Logotipo|Isto se o logótipo que pretende utilizar para este pacote. O tamanho do ícone é 32x32. Os formatos permitidos são PNG e JPG|
|Locais:Termos: PackageTerm.File|Este é o nome de ficheiro do doc HTML que contém os termos da sua licença.|

Aqui é onde o logotipo irá exibir:

![CRMScreenShot5](media/CRMScreenShot5.png)

O passo final é fechar o seguinte num ficheiro.

1. zip (criado anteriormente)
2. **Content_Types.xml**
3. xml
4. png
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

Mude o nome do ficheiro para algo apropriado para a sua aplicação. Preferimos que inclua o nome da sua empresa e o nome da aplicação. Por exemplo: **_Microsoft_SamplePackage.zip**.
