---
title: Criar um ASE com ARM
description: Aprenda a criar um ambiente de Serviço de Aplicações externo ou ILB utilizando um modelo de Gestor de Recursos Azure.
author: ccompy
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 15cd0979fdc2468ab50451042cd99a8442470139
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148176"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Crie um ASE utilizando um modelo de Gestor de Recursos Azure

## <a name="overview"></a>Descrição Geral

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Os ambientes do Azure App Service (ASEs) podem ser criados com um ponto final acessível à Internet ou um ponto final num endereço interno numa rede virtual Azure (VNet). Quando criado com um ponto final interno, este ponto final é fornecido por um componente Azure chamado equilibrador de carga interno (ILB). O ASE num endereço IP interno é chamado de ILB ASE. O ASE com um ponto final público é chamado de ASE Externo. 

Um ASE pode ser criado utilizando o portal Azure ou um modelo de Gestor de Recursos Azure. Este artigo percorre os passos e sintaxe que precisa para criar um ASE Externo ou ILB ASE com modelos de Gestor de Recursos. Para aprender a criar um ASE no portal Azure, consulte [Fazer um ASE Externo][MakeExternalASE] ou Fazer um [ASE ILB][MakeILBASE].

Quando criar um ASE no portal Azure, pode criar o seu VNet ao mesmo tempo ou escolher um VNet pré-existente para implantar. Quando criar um ASE a partir de um modelo, deve começar por: 

* Um gestor de recursos VNet.
* Uma sub-rede no VNet. Recomendamos um tamanho de sub-rede ASE `/24` com 256 endereços para acomodar necessidades futuras de crescimento e escala. Depois da criação do ASE, não se pode alterar o tamanho.
* O ID de recursos do seu VNet. Pode obter esta informação a partir do portal Azure sob as propriedades da sua rede virtual.
* A subscrição que pretende implementar.
* O local onde quer instalar.

Para automatizar a sua criação ASE:

1. Crie o ASE a partir de um modelo. Se criar um ASE externo, está acabado depois deste passo. Se criar um ILB ASE, há mais algumas coisas a fazer.

2. Após a criação do seu ILB ASE, é enviado um certificado TLS/SSL que corresponda ao seu domínio ILB ASE.

3. O certificado TLS/SSL carregado é atribuído ao ILB ASE como o seu certificado TLS/SSL "predefinido".  Este certificado é utilizado para o tráfego TLS/SSL para aplicações no ILB ASE quando utilizam o domínio raiz comum que é atribuído ao ASE (por exemplo, `https://someapp.mycustomrootdomain.com` ).


## <a name="create-the-ase"></a>Criar o ASE
Um modelo de Gestor de Recursos que cria um ficheiro ASE e os seus parâmetros associados está disponível [num exemplo][quickstartasev2create] no GitHub.

Se quiser fazer um ILB ASE, utilize estes [exemplos][quickstartilbasecreate]de modelo do Gestor de Recursos . Atendem a esse caso de uso. A maioria dos parâmetros do *azuredeploy.parameters.jsem* arquivo são comuns à criação de ASEs ILB e ASEs Externas. A seguinte lista chama parâmetros de nota especial, ou que são únicos, quando cria um ILB ASE:

* *internaLoadBalancingMode*: Na maioria dos casos, este é o 3, o que significa que tanto o tráfego HTTP/HTTPS nas portas 80/443, como as portas de canais de controlo/dados escutadas pelo serviço FTP na ASE, ficarão vinculadas a um endereço interno de rede virtual atribuído pelo ILB. Se esta propriedade estiver definida para 2, apenas as portas relacionadas com o serviço FTP (ambos os canais de controlo e dados) estão ligadas a um endereço ILB. O tráfego HTTP/HTTPS permanece no VIP público.
* *dnsS sufixo*: Este parâmetro define o domínio padrão da raiz que é atribuído ao ASE. Na variação pública do Azure App Service, o domínio padrão de raiz para todas as aplicações web é *azurewebsites.net*. Como um ILB ASE é interno na rede virtual de um cliente, não faz sentido usar o domínio de raiz padrão do serviço público. Em vez disso, um ILB ASE deve ter um domínio de raiz padrão que faça sentido para ser usado dentro da rede virtual interna de uma empresa. Por exemplo, a Contoso Corporation poderá utilizar um domínio de raiz padrão de *internal-contoso.com* para aplicações que se destinam a ser resolvidas e acessíveis apenas dentro da rede virtual de Contoso. 
* *ipSslAddressCount*: Este parâmetro automaticamente desrespeita a um valor de 0 no *azuredeploy.jsem* ficheiro porque as ASEs ILB têm apenas um único endereço ILB. Não existem endereços IP-SSL explícitos para um ILB ASE. Por conseguinte, o conjunto de endereços IP-SSL para um ILB ASE deve ser definido para zero. Caso contrário, ocorre um erro de provisionamento. 

Depois de preenchido o *azuredeploy.parameters.jsno* ficheiro, crie o ASE utilizando o corte de código PowerShell. Altere os caminhos do ficheiro para corresponder às localizações do ficheiro do modelo do Gestor de Recursos na sua máquina. Lembre-se de fornecer os seus próprios valores para o nome de implementação do Gestor de Recursos e o nome do grupo de recursos:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Leva cerca de uma hora para a ASE ser criada. Em seguida, o ASE aparece no portal na lista de ASEs para a subscrição que desencadeou a implementação.

## <a name="upload-and-configure-the-default-tlsssl-certificate"></a>Carregar e configurar o certificado TLS/SSL "predefinido"
Um certificado TLS/SSL deve ser associado ao ASE como o certificado TLS/SSL "predefinido" que é utilizado para estabelecer ligações TLS a apps. Se o sufixo DE DNS predefinido da ASE for *internal-contoso.com,* uma ligação `https://some-random-app.internal-contoso.com` requer um certificado TLS/SSL válido para **.internal-contoso.com*. 

Obter um certificado TLS/SSL válido utilizando as autoridades de certificados internos, comprando um certificado a um emitente externo ou utilizando um certificado auto-assinado. Independentemente da origem do certificado TLS/SSL, os seguintes atributos de certificado devem ser devidamente configurados:

* **Objeto**: Este atributo deve ser definido para **.your-root-domain-here.com*.
* **Nome Alternativo do Assunto**: Este atributo deve incluir ambos **.your-root-domain-here.com* e **.scm.your-root-domain-here.com*. As ligações TLS ao site SCM/Kudu associado a cada aplicação utilizam um endereço do formulário *your-app-name.scm.your-root-domain-here.com*.

Com um certificado TLS/SSL válido na mão, são necessários dois passos preparatórios adicionais. Converter/guardar o certificado TLS/SSL como ficheiro .pfx. Lembre-se que o ficheiro .pfx deve incluir todos os certificados intermédios e de raiz. Proteja-o com uma palavra-passe.

O ficheiro .pfx precisa de ser convertido numa cadeia base64 porque o certificado TLS/SSL é carregado utilizando um modelo de Gestor de Recursos. Como os modelos do Gestor de Recursos são ficheiros de texto, o ficheiro .pfx deve ser convertido numa cadeia base64. Desta forma pode ser incluído como um parâmetro do modelo.

Utilize o seguinte corte de código PowerShell para:

* Gere um certificado auto-assinado.
* Exporte o certificado como ficheiro .pfx.
* Converta o ficheiro .pfx numa cadeia codificada de base64.
* Guarde a cadeia codificada base64 para um ficheiro separado. 

Este código PowerShell para codificação base64 foi adaptado a partir do [blog de scripts PowerShell][examplebase64encoding]:

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

Depois de o certificado TLS/SSL ser gerado e convertido com sucesso para uma cadeia codificada de base64, utilize o modelo de gestor de recursos de exemplo [Configure o certificado SSL predefinido][quickstartconfiguressl] no GitHub. 

Os parâmetros do *azuredeploy.parameters.jsem* arquivo estão listados aqui:

* *appServiceEnvironmentName*: O nome do ILB ASE está a ser configurado.
* *ActualLocalização :* Cadeia de texto contendo a região de Azure onde foi implantado o ILB ASE.  Por exemplo: "South Central US".
* *pfxBlobString*: A representação de cordas codificada com base em 64 do ficheiro .pfx. Utilize o corte de código mostrado anteriormente e copie a cadeia contida em "exportedcert.pfx.b64". Cole-o como o valor do atributo *pfxBlobString.*
* *palavra-passe*: A palavra-passe utilizada para proteger o ficheiro .pfx.
* *certificaThumbprint*: A impressão digital do certificado. Se recuperar este valor da PowerShell (por exemplo, *$certificate. Impressão digital* do código anterior, pode usar o valor tal como está. Se copiar o valor da caixa de diálogo do certificado Do Windows, lembre-se de retirar os espaços extraérs. O *certificadoThumbprint* deve parecer-se com AF3143EB61D43F6727842115BB7F17BBCECAEE.
* *certificateName*: Um identificador de cordas amigável à sua escolha usado para identificar o certificado. O nome é usado como parte do identificador exclusivo do Gestor de Recursos para a entidade *Microsoft.Web/certificados* que representa o certificado TLS/SSL. O nome *deve* terminar com o seguinte sufixo: \_ yourASENameHere_InternalLoadBalancingASE. O portal Azure utiliza este sufixo como um indicador de que o certificado é utilizado para garantir um ASE ativado pelo ILB.

Um exemplo abreviado de *azuredeploy.parameters.jsé* mostrado aqui:

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

Após o *azuredeploy.parameters.jsno* ficheiro ser preenchido, configurar o certificado TLS/SSL predefinido utilizando o corte de código PowerShell. Altere os caminhos do ficheiro para corresponder onde os ficheiros do modelo do Gestor de Recursos estão localizados na sua máquina. Lembre-se de fornecer os seus próprios valores para o nome de implementação do Gestor de Recursos e o nome do grupo de recursos:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Leva cerca de 40 minutos por extremidade frontal ASE para aplicar a mudança. Por exemplo, para um ASE de tamanho predefinido que usa duas extremidades dianteiras, o modelo leva cerca de uma hora e 20 minutos para ser concluído. Enquanto o modelo está em execução, o ASE não consegue escalar.  

Após o fim do modelo, as aplicações no ILB ASE podem ser acedidas em HTTPS. As ligações são asseguradas utilizando o certificado TLS/SSL predefinido. O certificado TLS/SSL predefinido é utilizado quando as aplicações no ILB ASE são endereçadas utilizando uma combinação do nome da aplicação mais o nome de anfitrião predefinido. Por exemplo, `https://mycustomapp.internal-contoso.com` utiliza o certificado TLS/SSL predefinido para **.internal-contoso.com*.

No entanto, tal como as aplicações que funcionam no serviço multitenante público, os desenvolvedores podem configurar nomes de anfitriões personalizados para aplicações individuais. Também podem configurar vinculações únicas de certificado SNI TLS/SSL para aplicações individuais.

## <a name="app-service-environment-v1"></a>Ambiente do Serviço de Aplicações v1 ##
O Ambiente de Serviço de Aplicações tem duas versões: ASEv1 e ASEv2. As informações anteriores tiveram como base a versão ASEv2. Esta secção mostra as diferenças entre as versões ASEv1 e ASEv2.

No ASEv1, gere todos os recursos manualmente. Tal inclui os front-ends, os trabalhos e os endereços IP utilizados para SSL baseado em IP. Antes de poder escalar o seu plano de Serviço de Aplicações, tem de escalar a piscina de trabalhadores que pretende hospedar.

O ASEv1 utiliza um modelo de preços diferente do ASEv2. No ASEv1, paga por cada vCPU alocada. Isso inclui vCPUs que são usados para front ends ou trabalhadores que não estão hospedando nenhuma carga de trabalho. No ASEv1, o tamanho de dimensionamento máximo predefinido de um ASE é 55 anfitriões no total. Tal inclui os trabalhos e os front-ends. Uma vantagem do ASEv1 é que pode ser implementado numa rede virtual clássica e numa rede virtual do Resource Manager. Para saber mais sobre o ASEv1, veja o artigo [Introdução ao Ambiente de Serviço de Aplicações v1][ASEv1Intro].

Para criar um ASEv1 utilizando um modelo de Gestor de Recursos, consulte [Criar um ILB ASE v1 com um modelo de Gestor de Recursos][ILBASEv1Template].


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
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../../app-service/configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md