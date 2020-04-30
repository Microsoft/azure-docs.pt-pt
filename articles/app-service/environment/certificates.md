---
title: Encadernações de certificados
description: Explique inúmeros tópicos relacionados com certificados num Ambiente de Serviço de Aplicações. Saiba como funcionam as ligações de certificados nas aplicações uninadas numa ASE.
author: ccompy
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: dffa9571706c067834e47a656ec1d47cb884fb48
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82128714"
---
# <a name="certificates-and-the-app-service-environment"></a>Certificados e o Ambiente do Serviço de Aplicações 

O App Service Environment (ASE) é uma implantação do Serviço de Aplicações Azure que funciona dentro da sua Rede Virtual Azure (VNet). Pode ser implantado com um ponto final de aplicação acessível à Internet ou um ponto final de aplicação que está no seu VNet. Se implementar a ASE com um ponto final acessível à Internet, essa implementação é chamada de ASE Externa. Se implantar a ASE com um ponto final no seu VNet, essa implantação chama-se ILB ASE. Pode saber mais sobre o ILB ASE a partir do Create e utilizar um documento [ILB ASE.](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)

A ASE é um sistema de inquilinos único. Por ser inquilino único, existem algumas funcionalidades disponíveis apenas com uma ASE que não estão disponíveis no Serviço de Aplicações multi-inquilinos. 

## <a name="ilb-ase-certificates"></a>Certificados ILB ASE 

Se estiver a utilizar uma ASE Externa, então as suas aplicações são contactadas por [nome de aplicação]. [asename].p.azurewebsites.net. Por padrão, todas as ASEs, mesmo as ASLb, são criadas com certificados que seguem este formato. Quando se tem um ILB ASE, as aplicações são contactadas com base no nome de domínio que especifica ao criar o ILB ASE. Para que as aplicações suportem o TLS, é necessário fazer o upload de certificados. Obtenha um certificado TLS/SSL válido utilizando autoridades de certificados internos, adquirindo um certificado a um emitente externo ou utilizando um certificado auto-assinado. 

Existem duas opções para configurar certificados com o seu ILB ASE.  Pode definir um certificado de padrão wildcard para o ILB ASE ou definir certificados nas aplicações web individuais da ASE.  Independentemente da escolha que fizer, os seguintes atributos de certificado devem ser configurados corretamente:

- **Assunto:** Este atributo deve ser definido para *. [o seu domínio-raiz-aqui] para um certificado Wildcard ILB ASE. Se criar o certificado para a sua aplicação, então deve ser [nome de aplicação]. [o seu domínio-raiz-aqui]
- **Nome alternativo do sujeito:** Este atributo deve incluir ambos *. [o seu domínio-raiz-aqui] e *.scm. [o seu domínio-raiz-aqui] para o certificado wildcard ILB ASE. Se criar o certificado para a sua aplicação, então deve ser [nome de aplicação]. [o seu domínio-raiz-aqui] e [nome de aplicação].scm. [o seu domínio-raiz-aqui].

Como terceira variante, pode criar um certificado ILB ASE que inclui todos os nomes individuais de aplicações no SAN do certificado em vez de usar uma referência wildcard. O problema com este método é que precisa de saber antecipadamente os nomes das aplicações que está a colocar na ASE ou precisa de continuar a atualizar o certificado ILB ASE.

### <a name="upload-certificate-to-ilb-ase"></a>Certificado de upload para ILB ASE 

Depois de um ILB ASE ser criado no portal, o certificado deve ser definido para o ILB ASE. Até que o certificado seja definido, a ASE mostrará um banner que o certificado não foi definido.  

O certificado que envia deve ser um ficheiro .pfx. Após o upload do certificado, a ASE realizará uma operação à escala para definir o certificado. 

Não é possível criar a ASE e carregar o certificado como uma ação no portal ou mesmo num modelo. Como uma ação separada, pode carregar o certificado usando um modelo descrito no [Create a ASE a partir de um](./create-from-template.md) documento de modelo.  

Se pretender criar um certificado auto-assinado rapidamente para testes, pode utilizar a seguinte parte do PowerShell:

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
Ao criar um certificado auto-assinado, terá de garantir que o nome do assunto tem o formato de CN={ASE_NAME_HERE_InternalLoadBalancingASE.

## <a name="application-certificates"></a>Certificados de candidatura 

As aplicações que estão hospedadas numa ASE podem usar as funcionalidades de certificado sita-centrados em aplicativos que estão disponíveis no Serviço de Aplicações multi-inquilinos. Essas características incluem:  

- Certificados SNI 
- SSL baseado em IP, que só é suportado com uma ASE externa.  Um ILB ASE não suporta SSL baseado em IP.
- KeyVault recebeu certificados 

As instruções para o upload e gestão desses certificados estão disponíveis em [Adicionar um certificado TLS/SSL no Serviço de Aplicações Azure](../configure-ssl-certificate.md).  Se estiver simplesmente a configurar certificados para corresponder a um nome de domínio personalizado que atribuiu à sua aplicação web, essas instruções serão suficientes. Se estiver a carregar o certificado para uma aplicação web ILB ASE com o nome de domínio predefinido, especifique o site scm no SAN do certificado, tal como foi notado anteriormente. 

## <a name="tls-settings"></a>Definições tls 

Pode configurar a definição de TLS a um nível de aplicação.  

## <a name="private-client-certificate"></a>Certificado de cliente privado 

Um caso de uso comum é configurar a sua aplicação como cliente num modelo de servidor de cliente. Se você proteger o seu servidor com um certificado CA privado, você precisará enviar o certificado de cliente para a sua app.  As seguintes instruções carregarão certificados para a loja de fiduciários dos trabalhadores em que a sua aplicação está a funcionar. Se carregar o certificado para uma aplicação, pode usá-lo com as suas outras aplicações no mesmo plano de Serviço de Aplicações sem voltar a carregar o certificado.

Para fazer o upload do certificado para a sua aplicação na sua ASE:

1. Gere um ficheiro *.cer* para o seu certificado. 
2. Vá à app que precisa do certificado no portal Azure
3. Vá às definições ssl na aplicação. Clique em Carregar Certificado. Selecione Public. Selecione Local Machine. Forneça um nome. Navegue e selecione o seu ficheiro *.cer.* Selecione upload. 
4. Copie a impressão digital.
5. Ir para as Definições de Aplicação. Crie uma definição de aplicação WEBSITE_LOAD_ROOT_CERTIFICATES com a impressão digital como valor. Se tiver vários certificados, pode colocá-los no mesmo cenário separado por vírgulas e sem espaço branco como 

    84EC242A4EC7957817B8e48913E5552DAFA6,6A5C65DC9247f762FE17BF8D4906E4FE6B31819

O certificado estará disponível por todas as aplicações no mesmo plano de serviço de aplicações que a app, que configura essa definição. Se precisar que esteja disponível para apps num plano de Serviço de Aplicações diferente, terá de repetir a operação de Definição de Aplicações numa aplicação nesse plano de Serviço de Aplicações. Para verificar se o certificado está definido, vá à consola Kudu e emita o seguinte comando na consola de depuração PowerShell:

    dir cert:\localmachine\root

Para realizar testes, pode criar um certificado auto-assinado e gerar um ficheiro *.cer* com o seguinte PowerShell: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.cer"
    export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT

