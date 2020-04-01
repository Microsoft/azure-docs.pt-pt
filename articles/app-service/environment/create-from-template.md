---
title: Criar uma ASE com ARM
description: Aprenda a criar um ambiente de serviço de aplicações externo ou ILB utilizando um modelo de Gestor de Recursos Azure.
author: ccompy
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e06fcdbac097e85c039e34274c61cb51ee06bcd6
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478320"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Crie uma ASE usando um modelo de Gestor de Recursos Azure

## <a name="overview"></a>Descrição geral

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Os ambientes do Serviço de Aplicações Azure (ASE) podem ser criados com um ponto final acessível à Internet ou um ponto final num endereço interno numa rede virtual Azure (VNet). Quando criado com um ponto final interno, esse ponto final é fornecido por um componente Azure chamado um equilibrador de carga interna (ILB). A ASE num endereço IP interno chama-se ILB ASE. A ASE com um ponto final público chama-se ASE Externa. 

Uma ASE pode ser criada utilizando o portal Azure ou um modelo de Gestor de Recursos Azure. Este artigo percorre os passos e sintaxe que precisa para criar uma ASE Externa ou ILB ASE com modelos de Gestor de Recursos. Para aprender a criar uma ASE no portal Azure, consulte [Fazer uma ASE Externa][MakeExternalASE] ou Fazer uma [ASE ILB][MakeILBASE].

Quando cria uma ASE no portal Azure, pode criar o seu VNet ao mesmo tempo ou escolher um VNet pré-existente para se implantar. Quando cria uma ASE a partir de um modelo, tem de começar com: 

* Um gestor de recursos VNet.
* Uma sub-rede no VNet. Recomendamos uma sub-rede `/24` ASE com 256 endereços para acomodar futuras necessidades de crescimento e escala. Depois da Criação da ASE, não se pode alterar o tamanho.
* A identificação do recurso do seu VNet. Pode obter esta informação do portal Azure sob as suas propriedades de rede virtual.
* A subscrição que pretende implementar.
* A localização onde quer se deslocar.

Para automatizar a sua criação a ASE:

1. Crie a ASE a partir de um modelo. Se criar uma ASE externa, terminará depois deste passo. Se criares uma ASE ILB, há mais algumas coisas para fazer.

2. Após a criação do seu ILB ASE, é enviado um certificado TLS/SSL que corresponda ao seu domínio ILB ASE.

3. O certificado TLS/SSL carregado é atribuído ao ILB ASE como o seu certificado TLS/SSL "padrão".  Este certificado é utilizado para o tráfego TLS/SSL para aplicações no ILB ASE quando utilizam o `https://someapp.mycustomrootdomain.com`domínio raiz comum que é atribuído à ASE (por exemplo, ).


## <a name="create-the-ase"></a>Criar a ASE
Um modelo de Gestor de Recursos que cria uma ASE e o seu ficheiro de parâmetros associados está disponível [num exemplo][quickstartasev2create] no GitHub.

Se quiser fazer um ILB ASE, use estes [exemplos][quickstartilbasecreate]de modelo de Gestor de Recursos . Eles atendem a esse caso de uso. A maioria dos parâmetros no ficheiro *azuredeploy.parâmetros.json* são comuns à criação de AsEs ILB e ASEs Externos. A lista que se segue chama parâmetros de nota especial, ou que são únicos, quando cria um ILB ASE:

* *internalLoadBalancingMode*: Na maioria dos casos, o conjunto para 3, o que significa que tanto o tráfego HTTP/HTTPS nas portas 80/443, como as portas de controlo/canal de dados ouvidas pelo serviço FTP na ASE, serão vinculados a um endereço interno de rede virtual atribuído ao ILB. Se esta propriedade estiver definida para 2, apenas as portas relacionadas com o serviço FTP (tanto os canais de controlo como os canais de dados) estão ligados a um endereço ILB. O tráfego HTTP/HTTPS permanece no VIP público.
* *dnsSufix*: Este parâmetro define o domínio de raiz padrão atribuído à ASE. Na variação pública do Azure App Service, o domínio de raiz padrão para todas as aplicações web é *azurewebsites.net*. Como um ILB ASE é interno para a rede virtual de um cliente, não faz sentido usar o domínio de raiz padrão do serviço público. Em vez disso, um ILB ASE deve ter um domínio de raiz padrão que faça sentido para ser usado dentro da rede virtual interna de uma empresa. Por exemplo, a Corporação Contoso pode usar um domínio raiz padrão de *internal-contoso.com* para aplicações que se destinam a ser resolúveis e acessíveis apenas dentro da rede virtual de Contoso. 
* *ipSslAddressCount*: Este parâmetro falha automaticamente no valor de 0 no ficheiro *azuredeploy.json* porque o ILB ASEs tem apenas um único endereço ILB. Não existem endereços IP-SSL explícitos para um ILB ASE. Por conseguinte, o conjunto de endereços IP-SSL para um ILB ASE deve ser fixado a zero. Caso contrário, ocorre um erro de provisionamento. 

Depois de preenchido o ficheiro *azuredeploy.parâmetros.json,* crie a ASE utilizando o snippet de código PowerShell. Altere os caminhos de ficheiro para combinar com as localizações do ficheiro de modelo do Gestor de Recursos na sua máquina. Lembre-se de fornecer os seus próprios valores para o nome de implementação do Gestor de Recursos e o nome do grupo de recursos:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

A ASE demora cerca de uma hora a ser criada. Em seguida, a ASE aparece no portal na lista de ASEs para a subscrição que desencadeou a implantação.

## <a name="upload-and-configure-the-default-tlsssl-certificate"></a>Carregar e configurar o certificado TLS/SSL "predefinido"
Um certificado TLS/SSL deve ser associado à ASE como o certificado TLS/SSL "padrão" que é utilizado para estabelecer ligações TLS a apps. Se o sufixo DNS padrão da ASE `https://some-random-app.internal-contoso.com` for *internal-contoso.com,* uma ligação requer um certificado TLS/SSL válido para **.internal-contoso.com*. 

Obtenha um certificado TLS/SSL válido utilizando autoridades de certificados internos, adquirindo um certificado a um emitente externo ou utilizando um certificado auto-assinado. Independentemente da origem do certificado TLS/SSL, os seguintes atributos de certificado devem ser configurados corretamente:

* **Objeto:** Este atributo deve ser definido para **.your-root-domain-here.com*.
* **Nome alternativo**do sujeito: Este atributo deve incluir tanto **.your-root-domain-here.com* e **.scm.your-root-domain-here.com*. As ligações TLS ao site SCM/Kudu associados a cada aplicação utilizam um endereço do formulário *your-app-name.scm.your-root-domain-here.com*.

Com um certificado TLS/SSL válido na mão, são necessárias duas etapas preparatórias adicionais. Converter/guardar o certificado TLS/SSL como ficheiro .pfx. Lembre-se que o ficheiro .pfx deve incluir todos os certificados intermédios e de raiz. Proteja-o com uma palavra-passe.

O ficheiro .pfx precisa de ser convertido numa cadeia base64 porque o certificado TLS/SSL é carregado utilizando um modelo de Gestor de Recursos. Como os modelos do Gestor de Recursos são ficheiros de texto, o ficheiro .pfx deve ser convertido numa cadeia base64. Desta forma pode ser incluído como parâmetro do modelo.

Utilize o seguinte corte de código PowerShell para:

* Gere um certificado auto-assinado.
* Exporte o certificado como ficheiro .pfx.
* Converta o ficheiro .pfx numa cadeia codificada base64.
* Guarde a cadeia codificada base 64 para um ficheiro separado. 

Este código PowerShell para codificação base64 foi adaptado do blog de [scripts PowerShell:][examplebase64encoding]

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

Depois de o certificado TLS/SSL ser gerado com sucesso e convertido numa cadeia codificada com base64, utilize o modelo de gestor de recursos de exemplo [Configurar o certificado SSL predefinido][quickstartconfiguressl] no GitHub. 

Os parâmetros no ficheiro *azuredeploy.parâmetros.json* estão listados aqui:

* *appServiceEnvironmentName*: O nome da ASE ILB está a ser configurado.
* *aseLocalização existente*: Cadeia de texto contendo a região de Azure onde foi implantado o ILB ASE.  Por exemplo: "Centro-Sul dos EUA".
* *pfxBlobString*: A representação de cordas codificada por 64 4 do ficheiro .pfx. Utilize o código de corte mostrado anteriormente e copie a cadeia contida em "exportedcert.pfx.b64". Cola-o como o valor do atributo *pfxBlobString.*
* *palavra-passe*: A palavra-passe utilizada para proteger o ficheiro .pfx.
* *certificadoImpressão polegar*: A impressão digital do certificado. Se recuperar este valor da PowerShell (por exemplo, *$certificate. Impressão digital* digital do código anterior, pode usar o valor como está. Se copiar o valor da caixa de diálogo do certificado Windows, lembre-se de despir os espaços estranhos. O *certificadoImpressão Depolegar* deve parecer algo como AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *nome de certificado*: Um identificador de cordas amigável à sua escolha usado para identificar o certificado. O nome é usado como parte do identificador exclusivo do Gestor de Recursos para a entidade *Microsoft.Web/certificados* que representa o certificado TLS/SSL. O nome *deve* terminar com o \_seguinte sufixo: yourASENameHere_InternalLoadBalancingASE. O portal Azure utiliza este sufixo como um indicador de que o certificado é utilizado para garantir uma ASE ativada pelo ILB.

Um exemplo abreviado de *azuredeploy.parameters.json* é mostrado aqui:

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

Depois de preenchido o ficheiro *azuredeploy.parâmetros.json,* configure o certificado Padrão TLS/SSL utilizando o snippet do código PowerShell. Altere os caminhos de ficheiro para combinar onde os ficheiros do modelo Do Gestor de Recursos estão localizados na sua máquina. Lembre-se de fornecer os seus próprios valores para o nome de implementação do Gestor de Recursos e o nome do grupo de recursos:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Demora cerca de 40 minutos por extremidade frontal da ASE para aplicar a alteração. Por exemplo, para uma ASE de tamanho padrão que utiliza duas extremidades dianteiras, o modelo demora cerca de uma hora e 20 minutos a ser concluído. Enquanto o modelo está em execução, a ASE não pode escalar.  

Após o acabamento do modelo, as aplicações no ILB ASE podem ser acedidas em HTTPS. As ligações são seguras utilizando o certificado padrão TLS/SSL. O certificado Padrão TLS/SSL é utilizado quando as aplicações no ILB ASE são abordadas utilizando uma combinação do nome da aplicação mais o nome de anfitrião predefinido. Por exemplo, `https://mycustomapp.internal-contoso.com` utiliza o certificado Padrão TLS/SSL para **.internal-contoso.com*.

No entanto, tal como as aplicações que funcionam no serviço público multiarrendatário, os desenvolvedores podem configurar nomes de anfitriões personalizados para aplicações individuais. Também podem configurar ligações únicas de certificadoS SNI TLS/SSL para aplicações individuais.

## <a name="app-service-environment-v1"></a>Ambiente do Serviço de Aplicações v1 ##
O Ambiente de Serviço de Aplicações tem duas versões: ASEv1 e ASEv2. As informações anteriores tiveram como base a versão ASEv2. Esta secção mostra as diferenças entre as versões ASEv1 e ASEv2.

Na ASEv1, gere-se todos os recursos manualmente. Tal inclui os front-ends, os trabalhos e os endereços IP utilizados para SSL baseado em IP. Antes de poder esdimensionar o seu plano de Serviço de Aplicações, tem de escalar a piscina de trabalhadores que pretende acolher.

O ASEv1 utiliza um modelo de preços diferente do ASEv2. No ASEv1, paga por cada vCPU alocada. Isso inclui vCPUs que são usados para frente saem ou trabalhadores que não estão hospedando nenhuma carga de trabalho. No ASEv1, o tamanho de dimensionamento máximo predefinido de um ASE é 55 anfitriões no total. Tal inclui os trabalhos e os front-ends. Uma vantagem do ASEv1 é que pode ser implementado numa rede virtual clássica e numa rede virtual do Resource Manager. Para saber mais sobre o ASEv1, veja o artigo [Introdução ao Ambiente de Serviço de Aplicações v1][ASEv1Intro].

Para criar um ASEv1 utilizando um modelo de Gestor de Recursos, consulte [Criar um ILB ASE v1 com um modelo][ILBASEv1Template]de Gestor de Recursos .


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
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../../app-service/configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
