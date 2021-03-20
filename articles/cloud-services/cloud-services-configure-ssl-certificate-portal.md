---
title: Configure o TLS para um serviço de nuvem | Microsoft Docs
description: Saiba como especificar um ponto final HTTPS para uma função web e como carregar um certificado TLS/SSL para garantir a sua aplicação. Estes exemplos usam o portal Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 33aa088efd7768153d4a17472d82e0826f4ffa6b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742646"
---
# <a name="configuring-tls-for-an-application-in-azure"></a>Configurar TLS para uma aplicação em Azure

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

A Segurança da Camada de Transporte (TLS), anteriormente conhecida como encriptação Secure Socket Layer (SSL), é o método mais utilizado para proteger dados enviados através da internet. Esta tarefa comum discute como especificar um ponto final HTTPS para uma função web e como carregar um certificado TLS/SSL para garantir a sua aplicação.

> [!NOTE]
> Os procedimentos nesta tarefa aplicam-se aos Serviços Azure Cloud; para Serviços de Aplicações, veja [isto.](../app-service/configure-ssl-bindings.md)
>

Esta tarefa utiliza uma implantação de produção. As informações sobre a utilização de uma colocação de encenação são fornecidas no final deste tópico.

Leia [isto](cloud-services-how-to-create-deploy-portal.md) primeiro se ainda não tiver criado um serviço de cloud.

## <a name="step-1-get-a-tlsssl-certificate"></a>Passo 1: Obter um certificado TLS/SSL
Para configurar o TLS para um pedido, primeiro precisa de obter um certificado TLS/SSL assinado por uma Autoridade de Certificados (CA), um terceiro de confiança que emite certificados para o efeito. Se ainda não tiver um, precisa de obter um de uma empresa que venda certificados TLS/SSL.

O certificado deve satisfazer os seguintes requisitos relativos aos certificados TLS/SSL em Azure:

* O certificado deve conter uma chave pública.
* O certificado deve ser criado para troca de chaves, exportável para um ficheiro De troca de informações pessoais (.pfx).
* O nome do certificado deve corresponder ao domínio utilizado para aceder ao serviço de nuvem. Não é possível obter um certificado TLS/SSL de uma autoridade de certificados (CA) para o domínio cloudapp.net. Tem de adquirir um nome de domínio personalizado para utilizar ao aceder ao seu serviço. Quando solicita um certificado a um CA, o nome do sujeito do certificado deve corresponder ao nome de domínio personalizado utilizado para aceder à sua aplicação. Por exemplo, se o seu nome de domínio personalizado for **contoso.com** solicitaria um certificado da sua AC para **_contoso.com_* ou **www \. contoso.com**.
* O certificado deve utilizar um mínimo de encriptação de 2048 bits.

Para efeitos de teste, pode [criar](cloud-services-certs-create.md) e utilizar um certificado auto-assinado. Um certificado auto-assinado não é autenticado através de um CA e pode usar o domínio cloudapp.net como URL do site. Por exemplo, a seguinte tarefa utiliza um certificado auto-assinado no qual o nome comum (CN) utilizado no certificado é **sslexample.cloudapp.net**.

Em seguida, deve incluir informações sobre o certificado na definição de serviço e nos ficheiros de configuração do serviço.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Passo 2: Modificar a definição de serviço e os ficheiros de configuração
O seu pedido deve ser configurado para utilizar o certificado, e um ponto final HTTPS deve ser adicionado. Como resultado, a definição de serviço e os ficheiros de configuração de serviço precisam de ser atualizados.

1. No seu ambiente de desenvolvimento, abra o ficheiro de definição de serviço (CSDEF), adicione uma secção **de Certificados** na secção **WebRole** e inclua as seguintes informações sobre o certificado (e certificados intermédios):

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

   A secção **de Certificados** define o nome do nosso certificado, a sua localização e o nome da loja onde se encontra.

   As permissões `permissionLevel` (atributo) podem ser definidas com um dos seguintes valores:

   | Valor da Permissão | Description |
   | --- | --- |
   | limitedOrElevated |**(Predefinição)** Todos os processos de função podem aceder à chave privada. |
   | elevado |Só os processos elevados podem aceder à chave privada. |

2. No seu ficheiro de definição de serviço, adicione um elemento **InputEndpoint** na secção **Ponto final** para ativar HTTPS:

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

3. No seu ficheiro de definição de serviço, adicione um elemento **de ligação** na secção **'Sites'.** Este elemento adiciona uma ligação HTTPS para mapear o ponto final para o seu site:

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

   Todas as alterações necessárias ao ficheiro de definição de serviço foram concluídas; mas ainda precisa de adicionar as informações do certificado ao ficheiro de configuração de serviço.
4. No seu ficheiro de configuração de serviço (CSCFG), ServiceConfiguration.Cloud.cscfg, adicione um valor **certificado** com o do seu certificado. A seguinte amostra de código fornece detalhes da secção **certificados,** exceto o valor da impressão digital.

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

Agora que os ficheiros de definição de serviço e de configuração de serviço foram atualizados, embale a sua implementação para o upload para a Azure. Se estiver a utilizar **o cspack,** não utilize a bandeira **/generateConfigurationFile,** pois isso substituirá as informações do certificado que acabou de inserir.

## <a name="step-3-upload-a-certificate"></a>Passo 3: Carregar um certificado
Ligue-se ao portal Azure e...

1. Na secção **Todos os recursos** do Portal, selecione o seu serviço na nuvem.

    ![Publique o seu serviço na nuvem](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Clique em **Certificados**.

    ![Clique no ícone de certificados](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Clique em **Upload** no topo da área de certificados.

    ![Clique no item do menu upload](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Forneça o **Ficheiro**, **Palavra-passe** e clique em **Upload** na parte inferior da área de entrada de dados.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Passo 4: Ligar-se à instância de função utilizando HTTPS
Agora que a sua implantação está a funcionar em Azure, pode ligar-se a ele utilizando HTTPS.

1. Clique no URL do **Site** para abrir o navegador web.

   ![Clique no URL do site](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. No seu navegador web, modifique o link para utilizar **https** em vez de **http**, e depois visite a página.

   > [!NOTE]
   > Se estiver a utilizar um certificado auto-assinado, quando navegar num ponto final HTTPS que esteja associado ao certificado auto-assinado poderá ver um erro de certificado no navegador. A utilização de um certificado assinado por uma autoridade de certificação fidedigna elimina este problema; Entretanto, pode ignorar o erro. (Outra opção é adicionar o certificado auto-assinado à loja de certificados fidedigna do utilizador.)
   >
   >

   ![Pré-visualização do site](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Se quiser utilizar o TLS para uma colocação de encenação em vez de uma implantação de produção, primeiro terá de determinar o URL utilizado para a colocação de paragem. Uma vez implementado o seu serviço em nuvem, o URL para o ambiente de preparação é determinado pelo ID GUID **de implementação** neste formato: `https://deployment-id.cloudapp.net/`  
   >
   > Criar um certificado com o nome comum (CN) igual ao URL baseado em GUID (por exemplo, **328187776e774ceda8fc57609d404462.cloudapp.net**). Utilize o portal para adicionar o certificado ao seu serviço de nuvem encenada. Em seguida, adicione as informações do certificado aos seus ficheiros CSDEF e CSCFG, reembale a sua aplicação e atualize a sua implementação encenada para utilizar o novo pacote.
   >

## <a name="next-steps"></a>Passos seguintes
* [Configuração geral do seu serviço na nuvem](cloud-services-how-to-configure-portal.md).
* Saiba como [implementar um serviço de cloud](cloud-services-how-to-create-deploy-portal.md).
* Configure um [nome de domínio personalizado.](cloud-services-custom-domain-name-portal.md)
* [Gerencie o seu serviço na nuvem](cloud-services-how-to-manage-portal.md).



