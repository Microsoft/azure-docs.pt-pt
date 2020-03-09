---
title: Configure o SSL para um serviço na nuvem  Microsoft Docs
description: Saiba como especificar um ponto final HTTPS para uma função web e como carregar um certificado SSL para garantir a sua aplicação. Estes exemplos utilizam o portal Azure.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/26/2017
ms.author: tagore
ms.openlocfilehash: 6ddb7001f770a9d8aea38d1a4698e15c167aeaa4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385381"
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Configurar o SSL para uma aplicação no Azure

A encriptação Secure Socket Layer (SSL) é o método mais utilizado para proteger os dados enviados através da internet. Esta tarefa comum discute como especificar um ponto final HTTPS para uma função web e como carregar um certificado SSL para garantir a sua aplicação.

> [!NOTE]
> Os procedimentos nesta tarefa aplicam-se aos Serviços Azure Cloud; para serviços de aplicações, veja [isto](../app-service/configure-ssl-bindings.md).
>

Esta tarefa utiliza uma implantação de produção. As informações sobre a utilização de uma encenação são fornecidas no final deste tópico.

Leia [isto](cloud-services-how-to-create-deploy-portal.md) primeiro se ainda não criou um serviço na nuvem.

## <a name="step-1-get-an-ssl-certificate"></a>Passo 1: Obter um certificado SSL
Para configurar o SSL para um pedido, é necessário obter primeiro um certificado SSL que tenha sido assinado por uma Autoridade de Certificados (CA), um terceiro de confiança que emite certificados para o efeito. Se ainda não tem um, precisa de obter um de uma empresa que vende certificados SSL.

O certificado deve satisfazer os seguintes requisitos relativos aos certificados SSL no Azure:

* O certificado deve conter uma chave privada.
* O certificado deve ser criado para troca de chaves, exportável para um ficheiro de Intercâmbio de Informações Pessoais (.pfx).
* O nome do certificado deve corresponder ao domínio utilizado para aceder ao serviço de nuvem. Não é possível obter um certificado SSL de uma autoridade de certificados (CA) para o domínio cloudapp.net. Deve adquirir um nome de domínio personalizado para usar quando aceder ao seu serviço. Quando solicitar um certificado a um CA, o nome do certificado deve corresponder ao nome de domínio personalizado utilizado para aceder à sua aplicação. Por exemplo, se o seu nome de domínio personalizado for **contoso.com** solicitaria um certificado da sua CA para * **.contoso.com** ou **www\.contoso.com**.
* O certificado deve utilizar um mínimo de encriptação de 2048 bits.

Para efeitos de teste, pode [criar](cloud-services-certs-create.md) e utilizar um certificado auto-assinado. Um certificado auto-assinado não é autenticado através de um CA e pode usar o domínio cloudapp.net como URL do site. Por exemplo, a seguinte tarefa utiliza um certificado auto-assinado no qual o nome comum (NC) utilizado no certificado é **sslexample.cloudapp.net**.

Em seguida, deve incluir informações sobre o certificado na definição de serviço e ficheiros de configuração de serviço.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Passo 2: Modificar a definição de serviço e os ficheiros de configuração
A sua aplicação deve ser configurada para utilizar o certificado e deve ser adicionado um ponto final HTTPS. Como resultado, a definição de serviço e os ficheiros de configuração do serviço precisam de ser atualizados.

1. No seu ambiente de desenvolvimento, abra o ficheiro de definição de serviço (CSDEF), adicione uma secção de **Certificados** dentro da secção **WebRole** e inclua as seguintes informações sobre o certificado (e certificados intermédios):

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="My"
                        permissionLevel="limitedOrElevated" />
            <!-- IMPORTANT! Unless your certificate is either
            self-signed or signed directly by the CA root, you
            must include all the intermediate certificates
            here. You must list them here, even if they are
            not bound to any endpoints. Failing to list any of
            the intermediate certificates may cause hard-to-reproduce
            interoperability problems on some clients.-->
            <Certificate name="CAForSampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="CA"
                        permissionLevel="limitedOrElevated" />
        </Certificates>
    ...
    </WebRole>
    ```

   A secção **certificados** define o nome do nosso certificado, a sua localização e o nome da loja onde se encontra.

   Permissões (`permissionLevel` atributo) podem ser definidas para um dos seguintes valores:

   | Valor da Permissão | Descrição |
   | --- | --- |
   | limitedOrElevated |**(Predefinido)** Todos os processos de função podem aceder à chave privada. |
   | elevado |Só processos elevados podem aceder à chave privada. |

2. No ficheiro de definição de serviço, adicione um elemento **InputEndpoint** na secção **Pontos Finais** para ativar https:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Endpoints>
            <InputEndpoint name="HttpsIn" protocol="https" port="443"
                certificate="SampleCertificate" />
        </Endpoints>
    ...
    </WebRole>
    ```

3. No ficheiro de definição de serviço, adicione um elemento **Deligação** na secção **Sites.** Este elemento adiciona uma ligação HTTPS para mapear o ponto final do seu site:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="HttpsIn" endpointName="HttpsIn" />
                </Bindings>
            </Site>
        </Sites>
    ...
    </WebRole>
    ```

   Todas as alterações necessárias ao ficheiro de definição de serviço foram concluídas; mas, ainda precisa adicionar a informação do certificado ao ficheiro de configuração do serviço.
4. No seu ficheiro de configuração de serviço (CSCFG), ServiceConfiguration.Cloud.cscfg, adicione um valor de **Certificados** com o do seu certificado. A amostra de código que se segue fornece detalhes da secção **Certificados,** com exceção do valor da impressão digital.

   ```xml
    <Role name="Deployment">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff"
                thumbprintAlgorithm="sha1" />
            <Certificate name="CAForSampleCertificate"
                thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc"
                thumbprintAlgorithm="sha1" />
        </Certificates>
    ...
    </Role>
    ```

(Este exemplo usa **sha1** para o algoritmo de impressão digital. Especifique o valor adequado para o algoritmo de impressão digital do seu certificado.)

Agora que a definição de serviço e os ficheiros de configuração do serviço foram atualizados, embalem a sua implementação para o upload para o Azure. Se estiver a utilizar **o cspack,** não utilize a bandeira **/geração DeConfiguraçãoFile,** pois isso irá sobrepor-se à informação do certificado que acabou de inserir.

## <a name="step-3-upload-a-certificate"></a>Passo 3: Carregar um certificado
Ligue-se ao portal Azure e...

1. Na secção **De Todos os recursos** do Portal, selecione o seu serviço de nuvem.

    ![Publique o seu serviço na nuvem](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Clique em **Certificados**.

    ![Clique no ícone dos certificados](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Clique no **Upload** na parte superior da área de certificados.

    ![Clique no item do menu Upload](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Forneça o **Ficheiro**, **Palavra-passe**e clique em **Carregar** na parte inferior da área de entrada de dados.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Passo 4: Ligar à instância de funções utilizando HTTPS
Agora que a sua implementação está a funcionar em Azure, pode ligar-se a ela usando HTTPS.

1. Clique no URL do **Site** para abrir o navegador web.

   ![Clique no URL do Site](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. No seu navegador web, modifique o link para utilizar **https** em vez de **http**, e depois visite a página.

   > [!NOTE]
   > Se estiver a utilizar um certificado auto-assinado, quando navega para um ponto final HTTPS associado ao certificado auto-assinado, poderá ver um erro de certificado no navegador. A utilização de um certificado assinado por uma autoridade de certificação fidedigna elimina este problema; Entretanto, pode ignorar o erro. (Outra opção é adicionar o certificado auto-assinado à loja de certificados de fidedigno do utilizador.)
   >
   >

   ![Pré-visualização do site](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Se pretender utilizar o SSL para uma implantação de encenação em vez de uma implantação de produção, terá primeiro de determinar o URL utilizado para a implantação da encenação. Uma vez implementado o seu serviço de nuvem, o URL para o ambiente de paragem é determinado pelo ID DE **Implantação** GUID neste formato: `https://deployment-id.cloudapp.net/`  
   >
   > Crie um certificado com o nome comum (CN) igual ao URL baseado em GUID (por exemplo, **328187776e774ceda8fc57609d404462.cloudapp.net**). Utilize o portal para adicionar o certificado ao seu serviço de nuvem encenado. Em seguida, adicione as informações do certificado aos seus ficheiros CSDEF e CSCFG, reembala a sua aplicação e atualize a sua implementação faseada para utilizar o novo pacote.
   >

## <a name="next-steps"></a>Passos seguintes
* [Configuração geral do seu serviço](cloud-services-how-to-configure-portal.md)de nuvem.
* Aprenda a implementar um serviço de [nuvem.](cloud-services-how-to-create-deploy-portal.md)
* Configure um nome de [domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerencie o seu serviço de cloud](cloud-services-how-to-manage-portal.md).



