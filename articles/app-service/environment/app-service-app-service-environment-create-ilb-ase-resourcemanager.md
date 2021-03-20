---
title: Criar ILB ASE v1
description: Criar um ambiente de Serviço de Aplicações com um equilibrador de carga interno (ILB ASE). Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 2a03b791f37868010e107214ddcb7cf42174e4e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85833558"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Como Criar um ASE do ILB Utilizando Modelos do Azure Resource Manager

> [!NOTE] 
> Este artigo é sobre o App Service Environment v1. Existe uma versão mais recente do App Service Environment que é mais fácil de usar e funciona em infraestruturas mais poderosas. Para saber mais sobre a nova versão comece com a [Introdução ao Ambiente de Serviço de Aplicações.](intro.md)
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição Geral
Os Ambientes de Serviço de Aplicações podem ser criados com um endereço interno de rede virtual em vez de um VIP público.  Este endereço interno é fornecido por um componente Azure chamado o equilibrador interno de carga (ILB).  Um ILB ASE pode ser criado usando o portal Azure.  Também pode ser criado usando a automatização através de modelos Azure Resource Manager.  Este artigo percorre os passos e sintaxe necessários para criar um ILB ASE com modelos de Gestor de Recursos Azure.

Existem três etapas envolvidas na automatização da criação de um ILB ASE:

1. Primeiro, a base ASE é criada numa rede virtual utilizando um endereço de balançador de carga interno em vez de um VIP público.  Como parte deste passo, um nome de domínio raiz é atribuído ao ILB ASE.
2. Uma vez criado o ILB ASE, é carregado um certificado TLS/SSL.  
3. O certificado TLS/SSL carregado é explicitamente atribuído ao ILB ASE como o seu certificado TLS/SSL "predefinido".  Este certificado TLS/SSL será utilizado para o tráfego TLS para aplicações no ILB ASE quando as aplicações forem endereçadas utilizando o domínio raiz comum atribuído à ASE (por `https://someapp.mycustomrootcomain.com` exemplo)

## <a name="creating-the-base-ilb-ase"></a>Criação da Base ILB ASE
Um modelo de gestor de recursos Azure, e o seu ficheiro de parâmetros associados, estão disponíveis no GitHub [aqui.][quickstartilbasecreate]

A maioria dos parâmetros do *azuredeploy.parameters.jsem* arquivo são comuns para a criação tanto de ASEs ILB, como de ASEs ligadas a um VIP público.  A lista abaixo chama parâmetros de nota especial, ou que são únicos, ao criar um ILB ASE:

* *internaLoadBalancingMode*: Na maioria dos casos, o tráfego HTTP/HTTPS nas portas 80/443, e as portas de canais de controlo/dados ouvidas pelo serviço FTP na ASE, ficarão vinculadas a um endereço interno de rede virtual atribuído pelo ILB.  Se esta propriedade for definida para 2, então apenas as portas relacionadas com o serviço FTP (ambos os canais de controlo e dados) ficarão ligadas a um endereço ILB, enquanto o tráfego HTTP/HTTPS permanecerá no VIP público.
* *dnsS sufixo*: Este parâmetro define o domínio padrão da raiz que será atribuído ao ASE.  Na variação pública do Azure App Service, o domínio padrão de raiz para todas as aplicações web é *azurewebsites.net*.  No entanto, uma vez que um ILB ASE é interno na rede virtual de um cliente, não faz sentido usar o domínio de raiz padrão do serviço público.  Em vez disso, um ILB ASE deve ter um domínio de raiz padrão que faça sentido para ser usado dentro da rede virtual interna de uma empresa.  Por exemplo, uma hipotética Contoso Corporation pode usar um domínio raiz padrão de *internal-contoso.com* para apps que se destinam apenas a ser resolvidas e acessíveis dentro da rede virtual de Contoso. 
* *ipSslAddressCount*: Este parâmetro é automaticamente incumprido para um valor de 0 no *azuredeploy.jsem* ficheiro porque as ASEs ILB têm apenas um único endereço ILB.  Não existem endereços IP-SSL explícitos para um ILB ASE, pelo que o conjunto de endereços IP-SSL para um ILB ASE deve ser definido como zero, caso contrário, ocorrerá um erro de provisionamento. 

Uma vez preenchido o *azuredeploy.parameters.jsem* ficheiro para um ILB ASE, o ILB ASE pode então ser criado utilizando o seguinte corte de código PowerShell.  Altere os caminhos de ficheiro para corresponder onde os ficheiros do modelo do Gestor de Recursos Azure estão localizados na sua máquina.  Lembre-se também de fornecer os seus próprios valores para o nome de implementação do Gestor de Recursos Azure e nome do grupo de recursos.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Após a submissão do modelo Azure Resource Manager, levará algumas horas para que o ILB ASE seja criado.  Uma vez concluída a criação, o ILB ASE aparecerá no portal UX na lista de Ambientes de Serviço de Aplicações para a subscrição que desencadeou a implementação.

## <a name="uploading-and-configuring-the-default-tlsssl-certificate"></a>Upload e Configuração do Certificado TLS/SSL "Predefinido"
Uma vez criado o ILB ASE, um certificado TLS/SSL deve ser associado ao ASE como a utilização "predefinida" do certificado TLS/SSL para estabelecer ligações TLS/SSL a apps.  Continuando com o hipotético exemplo da Contoso Corporation, se o sufixo DNS padrão da ASE for *internal-contoso.com,* então uma ligação *`https://some-random-app.internal-contoso.com`* requer um certificado TLS/SSL válido para **.internal-contoso.com*. 

Existem várias formas de obter um certificado TLS/SSL válido, incluindo CAs internos, compra de um certificado a um emitente externo e utilização de um certificado auto-assinado.  Independentemente da origem do certificado TLS/SSL, os seguintes atributos de certificado devem ser configurados corretamente:

* *Objeto*: Este atributo deve ser definido para **.your-root-domain-here.com*
* *Nome Alternativo do Assunto*: Este atributo deve incluir ambos **.your-root-domain-here.com*, e **.scm.your-root-domain-here.com*.  A razão para a segunda entrada é que as ligações TLS ao site SCM/Kudu associadas a cada aplicação serão feitas usando um endereço do formulário *your-app-name.scm.your-root-domain-here.com*.

Com um certificado TLS/SSL válido na mão, são necessários dois passos preparatórios adicionais.  O certificado TLS/SSL tem de ser convertido/guardado como um ficheiro .pfx.  Lembre-se que o ficheiro .pfx precisa de incluir todos os certificados intermédios e de raiz, e também precisa de ser protegido com uma palavra-passe.

Em seguida, o ficheiro .pfx resultante precisa de ser convertido numa cadeia base64, porque o certificado TLS/SSL será carregado usando um modelo de Gestor de Recursos Azure.  Uma vez que os modelos do Gestor de Recursos Azure são ficheiros de texto, o ficheiro .pfx precisa de ser convertido numa cadeia base64 para que possa ser incluído como parâmetro do modelo.

O snippet de código PowerShell abaixo mostra um exemplo de gerar um certificado auto-assinado, exportar o certificado como um ficheiro .pfx, converter o ficheiro .pfx numa cadeia codificada base64 e, em seguida, guardar a cadeia codificada base64 para um ficheiro separado.  O código PowerShell para codificação base64 foi adaptado a partir do [PowerShell Scripts Blog][examplebase64encoding].

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

Uma vez que o certificado TLS/SSL tenha sido gerado e convertido com sucesso para uma cadeia codificada base64, o modelo de gestor de recursos Azure no GitHub para [configurar o certificado TLS/SSL predefinido][configuringDefaultSSLCertificate] pode ser utilizado.

Os parâmetros do *azuredeploy.parameters.jsem* arquivo são listados abaixo:

* *appServiceEnvironmentName*: O nome do ILB ASE está a ser configurado.
* *ActualLocalização :* Cadeia de texto contendo a região de Azure onde foi implantado o ILB ASE.  Por exemplo: "South Central US".
* *pfxBlobString*: A representação de cordas codificada com base 64 do ficheiro .pfx.  Utilizando o código de corte mostrado anteriormente, copiaria a cadeia contida em "exportedcert.pfx.b64" e colar-se-ia como o valor do atributo *pfxBlobString.*
* *palavra-passe*: A palavra-passe utilizada para proteger o ficheiro .pfx.
* *certificaThumbprint*: A impressão digital do certificado.  Se recuperar este valor da PowerShell (por *exemplo, $certificate. Impressão digital* do código anterior, pode usar o valor tal como está.  No entanto, se copiar o valor do diálogo do certificado do Windows, lembre-se de retirar os espaços extraéss.  O *certificadoThumbprint* deve ser semelhante: AF3143EB61D43F6727842115BB7F17BBCECAEE
* *certificateName*: Um identificador de cordas amigável à sua escolha usado para identificar o certificado.  O nome é usado como parte do identificador único do Azure Resource Manager para a entidade *Microsoft.Web/certificados* que representa o certificado TLS/SSL.  O nome **deve** terminar com o seguinte sufixo:  \_ yourASENameHere_InternalLoadBalancingASE.  Este sufixo é utilizado pelo portal como um indicador de que o certificado é utilizado para assegurar um ASE ativado pelo ILB.

Um exemplo abreviado de *azuredeploy.parameters.jsem* a seguir é mostrado abaixo:

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

Uma vez preenchido o *azuredeploy.parameters.jsno* ficheiro, o certificado TLS/SSL predefinido pode ser configurado utilizando o seguinte corte de código PowerShell.  Altere os caminhos de ficheiro para corresponder onde os ficheiros do modelo do Gestor de Recursos Azure estão localizados na sua máquina.  Lembre-se também de fornecer os seus próprios valores para o nome de implementação do Gestor de Recursos Azure e nome do grupo de recursos.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Após a submissão do modelo Azure Resource Manager, levará cerca de quarenta minutos por ase frontal para aplicar a alteração.  Por exemplo, com um ASE de tamanho padrão usando duas extremidades dianteiras, o modelo levará cerca de uma hora e vinte minutos para ser concluído.  Enquanto o modelo estiver a executar o ASE não será capaz de ser dimensionado.  

Uma vez que o modelo esteja concluído, as aplicações no ILB ASE podem ser acedidas através de HTTPS e as ligações serão protegidas usando o certificado TLS/SSL predefinido.  O certificado TLS/SSL predefinido será utilizado quando as aplicações no ILB ASE forem endereçadas utilizando uma combinação do nome da aplicação mais o nome de anfitrião predefinido.  Por exemplo, *`https://mycustomapp.internal-contoso.com`* utilizaria o certificado TLS/SSL predefinido para **.internal-contoso.com*.

No entanto, tal como as aplicações que executam no serviço público multi-inquilino, os desenvolvedores também podem configurar nomes de anfitriões personalizados para aplicações individuais e, em seguida, configurar vinculações únicas de certificado SNI TLS/SSL para aplicações individuais.  

## <a name="getting-started"></a>Introdução
Para começar com ambientes de serviço de aplicações, consulte [Introdução ao Ambiente de Serviço de Aplicações](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

