---
title: Criar ILB ASE v1
description: Crie um ambiente de Serviço de Aplicações com um equilibrador de carga interna (ILB ASE). Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: f05780610a2a6033b069721b143aca5e5efa6c35
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804525"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Como Criar um ASE do ILB Utilizando Modelos do Azure Resource Manager

> [!NOTE] 
> Este artigo é sobre o App Service Environment v1. Existe uma versão mais recente do App Service Environment que é mais fácil de usar e funciona em infraestruturas mais poderosas. Para saber mais sobre a nova versão comece com a Introdução ao Ambiente de Serviço de [Aplicações.](intro.md)
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral
Os Ambientes de Serviço de Aplicações podem ser criados com um endereço interno de rede virtual em vez de um VIP público.  Este endereço interno é fornecido por um componente Azure chamado balancedor de carga interna (ILB).  Um ILB ASE pode ser criado usando o portal Azure.  Também pode ser criado usando automação através de modelos de Gestor de Recursos Azure.  Este artigo percorre os passos e sintaxe necessários para criar um ILB ASE com modelos de Gestor de Recursos Azure.

Há três passos envolvidos na automatização da criação de um ILB ASE:

1. Primeiro a Base ASE é criada numa rede virtual utilizando um endereço de equilíbrio de carga interna em vez de um VIP público.  Como parte deste passo, um nome de domínio raiz é atribuído à ILB ASE.
2. Uma vez criado o ILB ASE, é enviado um certificado TLS/SSL.  
3. O certificado TLS/SSL carregado é explicitamente atribuído à ILB ASE como o seu certificado TLS/SSL "padrão".  Este certificado TLS/SSL será utilizado para o tráfego TLS para aplicações no ILB ASE quando as aplicações forem endereçadas utilizando o domínio raiz comum atribuído à ASE (por exemplo) `https://someapp.mycustomrootcomain.com`

## <a name="creating-the-base-ilb-ase"></a>Criação da Base ILB ASE
Um modelo de Gestor de Recursos Azure, e seu arquivo de parâmetros associados, estão disponíveis no GitHub [aqui][quickstartilbasecreate].

A maioria dos parâmetros no ficheiro *azuredeploy.parameters.json* são comuns à criação tanto de ASE ILB, como as ASEs ligadas a um VIP público.  A lista abaixo chama os parâmetros de nota especial, ou que são únicos, ao criar um ILB ASE:

* *internalLoadBalancingMode*: Na maioria dos casos, este ajuste para 3, o que significa que tanto o tráfego HTTP/HTTPS nas portas 80/443, como as portas de controlo/canal de dados ouvidas pelo serviço FTP na ASE, serão vinculados a um endereço interno de rede virtual iLB atribuído.  Se esta propriedade for definida para 2, então apenas as portas relacionadas com o serviço FTP (tanto os canais de controlo como os canais de dados) serão vinculados a um endereço ILB, enquanto o tráfego HTTP/HTTPS permanecerá no VIP público.
* *dnsSufix*: Este parâmetro define o domínio de raiz padrão que será atribuído à ASE.  Na variação pública do Azure App Service, o domínio de raiz padrão para todas as aplicações web é *azurewebsites.net*.  No entanto, uma vez que um ILB ASE é interno para a rede virtual de um cliente, não faz sentido usar o domínio raiz padrão do serviço público.  Em vez disso, um ILB ASE deve ter um domínio de raiz padrão que faça sentido para ser usado dentro da rede virtual interna de uma empresa.  Por exemplo, uma Corporação De Contoso hipotético pode usar um domínio raiz padrão de *internal-contoso.com* para aplicações que se destinam apenas a ser resolúveis e acessíveis dentro da rede virtual de Contoso. 
* *ipSslAddressCount*: Este parâmetro é automaticamente indefinido para um valor de 0 no ficheiro *azuredeploy.json* porque o ILB ASEs tem apenas um único endereço ILB.  Não existem endereços IP-SSL explícitos para um ILB ASE, pelo que o conjunto de endereços IP-SSL para um ILB ASE deve ser fixado a zero, caso contrário ocorrerá um erro de provisionamento. 

Uma vez preenchido o ficheiro *azuredeploy.parameters.json* para um ILB ASE, o ILB ASE pode então ser criado utilizando o seguinte corte de código Powershell.  Altere o ficheiro PATHs para combinar com o local onde os ficheiros do modelo Do Gestor de Recursos do Azure estão localizados na sua máquina.  Lembre-se também de fornecer os seus próprios valores para o nome de implantação do Gestor de Recursos Azure e nome de grupo de recursos.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Após a apresentação do modelo de Gestor de Recursos Azure, levará algumas horas para a criação do ILB ASE.  Assim que a criação estiver concluída, o ILB ASE aparecerá no portal UX na lista de Ambientes de Serviço de Aplicações para a subscrição que desencadeou a implementação.

## <a name="uploading-and-configuring-the-default-tlsssl-certificate"></a>Upload e Configuração do Certificado TLS/SSL "Padrão"
Uma vez criado o ILB ASE, um certificado TLS/SSL deve ser associado à ASE como a utilização "padrão" do certificado TLS/SSL para estabelecer ligações TLS/SSL às aplicações.  Continuando com o exemplo da Corporação Contoso, se o sufixo DNS *https://some-random-app.internal-contoso.com* padrão da ASE for *internal-contoso.com,* então uma ligação requer um certificado TLS/SSL válido para **.internal-contoso.com*. 

Existem várias formas de obter um certificado TLS/SSL válido, incluindo Os AC internos, a aquisição de um certificado a um emitente externo e a utilização de um certificado auto-assinado.  Independentemente da origem do certificado TLS/SSL, os seguintes atributos de certificado devem ser configurados corretamente:

* *Objeto:* Este atributo deve ser definido para **.your-root-domain-here.com*
* *Nome alternativo*do sujeito: Este atributo deve incluir ambos*os*your-root-domain-here.com , e **.scm.your-root-domain-here.com*.  A razão para a segunda entrada é que as ligações TLS ao site SCM/Kudu associados a cada aplicação serão feitas usando um endereço do formulário *your-app-name.scm.your-root-domain-here.com*.

Com um certificado TLS/SSL válido na mão, são necessárias duas etapas preparatórias adicionais.  O certificado TLS/SSL tem de ser convertido/guardado como ficheiro .pfx.  Lembre-se que o ficheiro .pfx precisa de incluir todos os certificados intermédios e radiculares, e também precisa de ser protegido com uma palavra-passe.

Em seguida, o ficheiro resultante .pfx precisa de ser convertido numa cadeia base64 porque o certificado TLS/SSL será carregado usando um modelo de Gestor de Recursos Azure.  Uma vez que os modelos do Gestor de Recursos Azure são ficheiros de texto, o ficheiro .pfx precisa de ser convertido numa cadeia base64 para que possa ser incluído como parâmetro do modelo.

O código Powershell abaixo mostra um exemplo de geração de um certificado auto-assinado, exportando o certificado como um ficheiro .pfx, convertendo o ficheiro .pfx numa cadeia codificada base64 e, em seguida, salvando a cadeia codificada base64 para um ficheiro separado.  O código Powershell para codificação base64 foi adaptado a partir do [Blog powershell Scripts][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Uma vez que o certificado TLS/SSL tenha sido gerado com sucesso e convertido para uma cadeia codificada base64, pode ser utilizado o modelo de Gestor de Recursos Azure no GitHub para [configurar o certificado Padrão TLS/SSL.][configuringDefaultSSLCertificate]

Os parâmetros do ficheiro *azuredeploy.parâmetros.json* estão listados abaixo:

* *appServiceEnvironmentName*: O nome da ASE ILB está a ser configurado.
* *aseLocalização existente*: Cadeia de texto contendo a região de Azure onde foi implantado o ILB ASE.  Por exemplo: "Centro-Sul dos EUA".
* *pfxBlobString*: A representação de cadeia codificada com base 64 do ficheiro .pfx.  Utilizando o código de corte mostrado anteriormente, copiaria a cadeia contida em "exportedcert.pfx.b64" e colaria-a como valor do atributo *pfxBlobString.*
* *palavra-passe*: A palavra-passe utilizada para proteger o ficheiro .pfx.
* *certificadoImpressão polegar*: A impressão digital do certificado.  Se recuperar este valor da Powershell (por exemplo, *$certificate. Impressão digital* digital do código anterior, pode usar o valor como está.  No entanto, se copiar o valor do diálogo do certificado Windows, lembre-se de despir os espaços estranhos.  O *certificadoImpressão de polegar* deve ser algo parecido com: AF3143EB61D43F6727842115BB7F17BBCECAECAEE
* *nome de certificado*: Um identificador de cordas amigável à sua escolha usado para identificar o certificado.  O nome é usado como parte do identificador único do Gestor de Recursos Azure para a entidade *Microsoft.Web/certificados* que representam o certificado TLS/SSL.  O nome **deve** terminar com o \_seguinte sufixo: yourASENameHere_InternalLoadBalancingASE.  Este sufixo é utilizado pelo portal como um indicador de que o certificado é utilizado para assegurar uma ASE ativada pelo ILB.

Um exemplo abreviado de *azuredeploy.parameters.json* é mostrado abaixo:

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

Uma vez preenchido o ficheiro *azuredeploy.parâmetros.json,* o certificado Padrão TLS/SSL pode ser configurado utilizando o seguinte corte de código Powershell.  Altere o ficheiro PATHs para combinar com o local onde os ficheiros do modelo Do Gestor de Recursos do Azure estão localizados na sua máquina.  Lembre-se também de fornecer os seus próprios valores para o nome de implantação do Gestor de Recursos Azure e nome de grupo de recursos.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Após a apresentação do modelo do Gestor de Recursos Azure, levará cerca de quarenta minutos por aase frontal para aplicar a alteração.  Por exemplo, com uma ASE de tamanho padrão utilizando duas extremidades dianteiras, o modelo levará cerca de uma hora e vinte minutos para ser concluído.  Enquanto o modelo estiver em execução, a ASE não será capaz de escalar.  

Uma vez que o modelo esteja concluído, as aplicações no ILB ASE podem ser acedidas através de HTTPS e as ligações serão protegidas usando o certificado Padrão TLS/SSL.  O certificado Padrão TLS/SSL será utilizado quando as aplicações do ILB ASE forem abordadas utilizando uma combinação do nome da aplicação mais o nome de anfitrião predefinido.  Por *https://mycustomapp.internal-contoso.com* exemplo, utilizaria o certificado padrão TLS/SSL para **.internal-contoso.com*.

No entanto, tal como as aplicações que estão a funcionar no serviço público multi-inquilinos, os desenvolvedores também podem configurar nomes de anfitriões personalizados para aplicações individuais e, em seguida, configurar ligações únicas de certificados SNI TLS/SSL para aplicações individuais.  

## <a name="getting-started"></a>Introdução
Para começar com ambientes de serviço de aplicações, consulte [Introdução ao Ambiente de Serviço de Aplicações](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

