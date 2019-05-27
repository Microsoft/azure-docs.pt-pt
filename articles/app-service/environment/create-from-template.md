---
title: Criar o ambiente de serviço de aplicações com o modelo do Resource Manager - Azure
description: Explica como criar um ambiente externo ou o serviço de aplicações do Azure ILB utilizando um modelo do Resource Manager
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: bdf722ffa7a7c499ff256392886e0f229f27c7a5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66137078"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Criar um ASE com um modelo Azure Resource Manager

## <a name="overview"></a>Descrição geral

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ambientes de serviço de aplicações (ASEs) do Azure podem ser criados com um ponto final acessível pela internet ou de um ponto final num endereço interno numa rede virtual do Azure (VNet). Quando é criado com um ponto final interno, o ponto de extremidade é fornecida por um Azure componente chamado de um balanceador de carga interno (ILB). O ASE num endereço IP interno é chamado de um ASE de ILB. O ASE com um ponto final público é chamado ASE externo. 

Um ASE pode ser criado utilizando o portal do Azure ou um modelo Azure Resource Manager. Este artigo explica os passos e a sintaxe que tem de criar um ASE externo ou o ASE de ILB com modelos do Resource Manager. Para saber como criar um ASE no portal do Azure, veja [tornar um ASE externo] [ MakeExternalASE] ou [tornar um ASE de ILB][MakeILBASE].

Ao criar um ASE no portal do Azure, pode criar a sua VNet ao mesmo tempo ou escolha uma VNet preexistente para implementar numa. Quando criar um ASE a partir de um modelo, tem de começar com: 

* A Resource Manager VNet.
* Uma sub-rede nessa vnet. Recomendamos um tamanho de sub-rede do ASE de `/24` com 256 endereços para acomodar o crescimento futuro e as necessidades de dimensionamento. Após a criação do ASE, não é possível alterar o tamanho.
* O ID de recurso da sua VNet. Pode obter estas informações no portal do Azure nas suas propriedades de rede virtual.
* A subscrição que pretende implementar.
* A localização que pretende implementar.

Para automatizar a criação do ASE:

1. Crie ASE a partir de um modelo. Se criar um ASE externo, terá terminado após este passo. Se criar um ASE de ILB, existem algumas coisas a fazer.

2. Depois do ASE de ILB é criado, é carregado um certificado SSL que corresponda ao seu domínio do ASE de ILB.

3. O certificado SSL carregado é atribuído para o ASE de ILB, como o seu certificado SSL de "predefinição".  Este certificado é utilizado para tráfego SSL às aplicações num ASE de ILB quando estiverem a utilizar o domínio de raiz comuns, que é atribuído para o ASE (por exemplo, https://someapp.mycustomrootdomain.com).


## <a name="create-the-ase"></a>Criar o ASE
Um modelo do Resource Manager que cria um ASE e o respetivo ficheiro de parâmetros associados está disponível [um exemplo] [ quickstartasev2create] no GitHub.

Se quiser fazer um ASE de ILB, utilize estes modelo do Resource Manager [exemplos][quickstartilbasecreate]. Eles se adaptar a que o caso de utilização. A maioria dos parâmetros a *azuredeploy* ficheiro são comuns para a criação dos ASEs do ILB e os ASEs externo. A lista a seguir chama parâmetros de destacar ou que são exclusivos, ao criar um ASE de ILB:

* *internalLoadBalancingMode*: Na maioria dos casos, conjunto esta opção para 3, o que significa que o tráfego HTTP/HTTPS na portas 80/443 e os controle/dados portas de canal aberta pelo serviço FTP sobre o ASE, sejam ligados com um endereço de ILB alocada a rede virtual. Se esta propriedade é definida como 2, apenas as com o serviço portas de FTP (canais de controlo e dados) estão vinculadas a um endereço ILB. O tráfego HTTP/HTTPS permanece no VIP público.
* *dnsSuffix*: Este parâmetro define o domínio de raiz predefinido que é atribuído para o ASE. A variação pública do serviço de aplicações do Azure, o domínio de raiz predefinida para todas as web apps é *azurewebsites.net*. Como um ASE de ILB é interno para a rede virtual de um cliente, não faz sentido usar o domínio de raiz do serviço público predefinido. Em vez disso, um ASE de ILB deve ter um domínio de raiz predefinida que faça sentido para utilização na rede virtual interna de uma empresa. Por exemplo, Contoso Corporation poderá utilizar um domínio de raiz predefinida da *interna contoso.com* para aplicações que se destinam a ser resolvido e acessível apenas numa rede virtual da Contoso. 
* *ipSslAddressCount*: Este parâmetro é automaticamente predefinido para um valor de 0 no *azuredeploy. JSON* ficheiros porque os ASEs do ILB tem apenas um único endereço ILB. Não há nenhum endereço IP SSL explícito para um ASE de ILB. Por conseguinte, o conjunto de endereços IP SSL para um ASE de ILB tem de ser definido como zero. Caso contrário, ocorre um erro de aprovisionamento. 

Depois do *azuredeploy* ficheiro é preenchido, criar o ASE com o trecho de código do PowerShell. Altere os caminhos de ficheiro para corresponder as localizações de ficheiro de modelo do Resource Manager no seu computador. Não se esqueça de fornecer seus próprios valores para o nome da implementação do Resource Manager e o nome do grupo de recursos:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Demora cerca de uma hora para o ASE a ser criada. Em seguida, o ASE aparece no portal na lista dos ASEs para a subscrição que disparou a implementação.

## <a name="upload-and-configure-the-default-ssl-certificate"></a>Carregar e configurar o certificado SSL "predefinido"
Um certificado SSL deve ser associado com o ASE como o certificado SSL de "predefinição" que é utilizado para estabelecer ligações SSL para aplicações. Se for de sufixo DNS predefinido o ASE *interna contoso.com*, uma ligação ao https://some-random-app.internal-contoso.com requer um certificado SSL que é válido para o **.internal contoso.com*. 

Obter um certificado SSL válido ao utilizar autoridades de certificação internas, adquirir um certificado de um emissor externo ou utilizar um certificado autoassinado. Independentemente da origem do certificado SSL, os seguintes atributos de certificado tem de ser configurados corretamente:

* **Assunto**: Este atributo deve ser definido como **.your-raiz-domínio-here.com*.
* **Nome alternativo do requerente**: Este atributo tem de incluir **.your-raiz-domínio-here.com* e **Here-raiz-domínio-here.com*. Ligações de SSL ao site SCM/Kudu associadas a cada aplicação utilizam um endereço do formulário *your-app-name.scm.your-root-domain-here.com*.

Com um certificado SSL válido em mãos, são necessárias duas etapas preparatórias adicionais. Converta/guarde o certificado SSL como um ficheiro .pfx. Lembre-se de que o ficheiro. pfx tem de incluir todos os intermediário e certificados de raiz. Proteja-o com uma palavra-passe.

O ficheiro. pfx tem de ser convertido numa cadeia base64 porque o certificado SSL é carregado com um modelo do Resource Manager. Como os modelos do Resource Manager são ficheiros de texto, o ficheiro. pfx deve ser convertido numa cadeia base64. Desta forma pode ser incluído como um parâmetro do modelo.

Utilize o seguinte fragmento de código do PowerShell para:

* Gere um certificado autoassinado.
* Exporte o certificado como um ficheiro. pfx.
* Converta o ficheiro. pfx numa cadeia de caracteres codificada em base64.
* Guarde a cadeia de caracteres codificada em base64 para um arquivo separado. 

Este código de PowerShell para a codificação base64 foi adaptado do [blog de scripts do PowerShell][examplebase64encoding]:

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

Depois do certificado SSL é gerado e convertido numa cadeia com codificação base64 com êxito, utilize o modelo do Resource Manager de exemplo [configurar o certificado SSL predefinido] [ quickstartconfiguressl] no GitHub. 

Os parâmetros nos *azuredeploy* arquivo estão listados aqui:

* *appServiceEnvironmentName*: O nome do ASE de ILB a ser configurado.
* *existingAseLocation*: Cadeia de texto que contém a região do Azure em que o ASE de ILB foi implementado.  Por exemplo: "Sul dos E.U.A.".
* *pfxBlobString*: A representação de cadeia com codificação based64 do ficheiro. pfx. Utilize o fragmento de código mostrado anteriormente e copie a cadeia de caracteres contida no "exportedcert.pfx.b64". Cole-a no como o valor do *pfxBlobString* atributo.
* *palavra-passe*: A palavra-passe utilizada para proteger o ficheiro. pfx.
* *certificateThumbprint*: Thumbprint do certificado. Se recuperar esse valor a partir do PowerShell (por exemplo, *$certificate. Thumbprint* do trecho de código anterior), pode usar o valor como está. Se copiar o valor da caixa de diálogo de certificado do Windows, não se esqueça de retirar os espaços estranhos. O *certificateThumbprint* deve ser algo semelhante AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName*: Utilizado um identificador de cadeia de caracteres amigável seus próprios escolher para identidade o certificado. O nome é utilizado como parte do identificador exclusivo do Resource Manager para o *Microsoft.Web/certificates* entidade que representa o certificado SSL. O nome *tem* terminar com o seguinte sufixo: \_yourASENameHere_InternalLoadBalancingASE. O portal do Azure utiliza este sufixo como um indicador de que o certificado é utilizado para proteger um ASE com ILB ativado.

Um exemplo abreviado *azuredeploy* é mostrado aqui:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appServiceEnvironmentName": {
      "value": "yourASENameHere"
    },
    "existingAseLocation": {
      "value": "East US 2"
    },
    "pfxBlobString": {
      "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
    },
    "password": {
      "value": "PASSWORDGOESHERE"
    },
    "certificateThumbprint": {
      "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
    },
    "certificateName": {
      "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
    }
  }
}
```

Depois do *azuredeploy* ficheiro é preenchido, configure o certificado SSL predefinido ao utilizar o trecho de código do PowerShell. Altere os caminhos de ficheiro para corresponder ao onde os arquivos de modelo do Resource Manager estão localizados no seu computador. Não se esqueça de fornecer seus próprios valores para o nome da implementação do Resource Manager e o nome do grupo de recursos:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Demora cerca de 40 minutos por front-end de ASE para aplicar a alteração. Por exemplo, para um ASE de tamanho padrão que utiliza dois front-ends, o modelo demora aproximadamente uma hora e 20 minutos a concluir. Enquanto o modelo está em execução, não é possível dimensionar o ASE.  

Após a conclusão do modelo, as aplicações no ASE de ILB podem ser acedidas através de HTTPS. As ligações são protegidas com o certificado SSL predefinido. O certificado SSL predefinido é utilizado quando aplicações num ASE de ILB são resolvidas ao utilizar uma combinação de nome da aplicação e o nome de anfitrião predefinido. Por exemplo, https://mycustomapp.internal-contoso.com utiliza o certificado SSL predefinido para **.internal contoso.com*.

No entanto, assim como aplicações que são executadas no serviço público multi-inquilino, os desenvolvedores podem configurar nomes de anfitrião personalizado para aplicações individuais. Também pode configurar exclusivos enlaces de certificado de SNI SSL para aplicações individuais.

## <a name="app-service-environment-v1"></a>Ambiente do Serviço de Aplicações v1 ##
Ambiente de serviço de aplicações tem duas versões: ASEv1 e ASEv2. As informações anteriores tiveram como base a versão ASEv2. Esta secção mostra as diferenças entre as versões ASEv1 e ASEv2.

No ASEv1, gere todos os recursos manualmente. Tal inclui os front-ends, os trabalhos e os endereços IP utilizados para SSL baseado em IP. Antes do utilizador pode ampliar o seu plano do serviço de aplicações, deve aumentar horizontalmente o conjunto de trabalho que pretende alojá-lo.

O ASEv1 utiliza um modelo de preços diferente do ASEv2. No ASEv1, paga por cada vCPU alocada. Tal inclui as vCPUs utilizadas para front-ends ou trabalhos que não estejam a alojar quaisquer cargas de trabalho. No ASEv1, o tamanho de dimensionamento máximo predefinido de um ASE é 55 anfitriões no total. Tal inclui os trabalhos e os front-ends. Uma vantagem do ASEv1 é que pode ser implementado numa rede virtual clássica e numa rede virtual do Resource Manager. Para saber mais sobre o ASEv1, veja o artigo [Introdução ao Ambiente de Serviço de Aplicações v1][ASEv1Intro].

Para criar um ASEv1 utilizando um modelo do Resource Manager, veja [criar um v1 do ASE de ILB com um modelo do Resource Manager][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service/web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
