---
title: Encadernações de certificados
description: Explique inúmeros tópicos relacionados com certificados num Ambiente de Serviço de Aplicações. Saiba como funcionam as obrigações de certificados nas aplicações de um único inquilino numa ASE.
author: ccompy
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 306445e26e5b236b49273b9ab8888ecc610bc075
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88962048"
---
# <a name="certificates-and-the-app-service-environment"></a>Certificados e o Ambiente de Serviço de Aplicações 

O App Service Environment (ASE) é uma implementação do Azure App Service que funciona dentro da sua Rede Virtual Azure (VNet). Pode ser implantado com um ponto final de aplicação acessível à Internet ou um ponto final de aplicação que está no seu VNet. Se implementar o ASE com um ponto final acessível à Internet, essa implementação é chamada de ASE Externa. Se colocar o ASE com um ponto final no seu VNet, essa implementação chama-se ILB ASE. Pode saber mais sobre o ILB ASE a partir do Create e utilizar um documento [ILB ASE.](./create-ilb-ase.md)

O ASE é um sistema de inquilinos únicos. Por ser inquilino único, existem algumas funcionalidades disponíveis apenas com um ASE que não estão disponíveis no Serviço de Aplicações multi-arrendatários. 

## <a name="ilb-ase-certificates"></a>Certificados ILB ASE 

Se estiver a utilizar um ASE Externo, então as suas aplicações são alcançadas [appname]. [asename].p.azurewebsites.net. Por padrão, todas as ASEs, mesmo as ASEs ILB, são criadas com certificados que seguem este formato. Quando tem um ILB ASE, as aplicações são alcançadas com base no nome de domínio que especifica ao criar o ILB ASE. Para que as aplicações suportem o TLS, é necessário fazer o upload dos certificados. Obter um certificado TLS/SSL válido utilizando as autoridades de certificados internos, comprando um certificado a um emitente externo ou utilizando um certificado auto-assinado. 

Existem duas opções para configurar certificados com o seu ILB ASE.  Pode definir um certificado de incumprimento para o ILB ASE ou definir certificados nas aplicações web individuais da ASE.  Independentemente da escolha que faça, os seguintes atributos de certificado devem ser configurados corretamente:

- **Objeto:** Este atributo deve ser definido para *. [o seu domínio-raiz-aqui] para um certificado ILB ASE wildcard. Se criar o certificado para a sua aplicação, então deve ser [nome de app]. [o seu domínio-raiz-aqui]
- **Nome alternativo do sujeito:** Este atributo deve incluir ambos *. [o seu domínio-raiz-aqui] e *.scm. [o seu domínio-raiz-aqui] para o certificado wildcard ILB ASE. Se criar o certificado para a sua aplicação, então deve ser [nome de app]. [o seu domínio-raiz-aqui] e [appname].scm. [o seu domínio-raiz-aqui].

Como terceira variante, pode criar um certificado ILB ASE que inclui todos os nomes de aplicações individuais na SAN do certificado em vez de usar uma referência wildcard. O problema com este método é que precisa de saber antes dos nomes das aplicações que está a colocar na ASE ou precisa de continuar a atualizar o certificado ILB ASE.

### <a name="upload-certificate-to-ilb-ase"></a>Certificado de upload para ILB ASE 

Após a criação de um ILB ASE no portal, o certificado deve ser definido para o ILB ASE. Até que o certificado esteja definido, a ASE mostrará um banner de que o certificado não foi definido.  

O certificado que envia deve ser um ficheiro .pfx. Após o upload do certificado, a ASE realizará uma operação de escala para definir o certificado. 

Não é possível criar o ASE e fazer o upload do certificado como uma ação no portal ou mesmo num modelo. Como uma ação separada, você pode carregar o certificado usando um modelo como descrito no [Create a ASE a partir de um](./create-from-template.md) documento de modelo.  

Se pretender criar rapidamente um certificado auto-assinado para testes, pode utilizar o seguinte bit de PowerShell:

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password
```

Ao criar um certificado auto-assinado, terá de garantir que o nome do sujeito tem o formato de CN={ASE_NAME_HERE}_InternalLoadBalancingASE.

## <a name="application-certificates"></a>Certificados de inscrição 

As aplicações que estão hospedadas num ASE podem usar as funcionalidades de certificado centrado em aplicações que estão disponíveis no Serviço de Aplicações multi-inquilinos. Estas características incluem:  

- Certificados SNI 
- SSL baseado em IP, que só é suportado com um ASE externo.  Um ILB ASE não suporta SSL baseado em IP.
- Certificados keyVault hospedados 

As instruções para o upload e gestão desses certificados estão disponíveis no [Add a TLS/SSL certificate in Azure App Service](../configure-ssl-certificate.md).  Se estiver simplesmente a configurar certificados para corresponder a um nome de domínio personalizado que atribuiu à sua aplicação web, então essas instruções serão suficientes. Se estiver a enviar o certificado para uma aplicação web ILB ASE com o nome de domínio predefinido, especifique o site scm na SAN do certificado, como indicado anteriormente. 

## <a name="tls-settings"></a>Definições de TLS 

Pode configurar a definição de TLS a nível de aplicação.  

## <a name="private-client-certificate"></a>Certificado de cliente privado 

Um caso de uso comum é configurar a sua aplicação como cliente num modelo de servidor de cliente. Se proteger o seu servidor com um certificado de CA privado, terá de enviar o certificado do cliente para a sua aplicação.  As seguintes instruções carregarão certificados para a loja de fidese dos trabalhadores em que a sua aplicação está em funcionamento. Se carregar o certificado numa aplicação, pode usá-lo com as suas outras aplicações no mesmo plano de Serviço de Aplicações sem voltar a carregar o certificado.

Para fazer o upload do certificado para a sua aplicação na sua ASE:

1. Gere um *ficheiro .cer* para o seu certificado. 
2. Vá à app que precisa do certificado no portal Azure
3. Aceda às definições SSL na aplicação. Clique em Carregar Certificado. Selecione Público. Selecione Máquina Local. Forneça um nome. Navegue e selecione o seu ficheiro *.cer.* Selecione upload. 
4. Copie a impressão digital.
5. Aceda às Definições de Aplicação. Crie uma configuração de WEBSITE_LOAD_ROOT_CERTIFICATES de aplicação com a impressão digital como valor. Se tiver vários certificados, pode colocá-los na mesma definição separada por vírgulas e nenhum espaço em branco como 

    84EC242A4EC7957817B8E48913E5095552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

O certificado estará disponível por todas as aplicações no mesmo plano de serviço de aplicações que a app, que configurava essa configuração. Se precisar que esteja disponível para aplicações num plano de Serviço de Aplicações diferente, terá de repetir a operação de Definição de Aplicações numa aplicação nesse plano de Serviço de Aplicações. Para verificar se o certificado está definido, vá à consola Kudu e emita o seguinte comando na consola PowerShell depurg:

```azurepowershell-interactive
dir cert:\localmachine\root
```

Para realizar testes, pode criar um certificado auto-assinado e gerar um ficheiro *.cer* com o seguinte PowerShell: 

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.cer"
export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT
```