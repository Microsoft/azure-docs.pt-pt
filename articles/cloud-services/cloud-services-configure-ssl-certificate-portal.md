---
title: Configurar SSL para um serviço de nuvem | Microsoft Docs
description: Saiba como especificar um ponto de extremidade HTTPS para uma função Web e como carregar um certificado SSL para proteger seu aplicativo. Esses exemplos usam o portal do Azure.
services: cloud-services
documentationcenter: .net
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 05/26/2017
ms.author: gwallace
ms.openlocfilehash: 9e7b7526f13fa6b9ae648c4ddb4004a627d85154
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359747"
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Configurando o SSL para um aplicativo no Azure

A encriptação Secure Socket Layer (SSL) é o método mais utilizado para proteger dados enviados através da Internet. Esta tarefa comum debate como especificar um ponto final HTTPS para uma função Web e como carregar um certificado SSL para proteger a sua aplicação.

> [!NOTE]
> Os procedimentos nesta tarefa se aplicam aos serviços de nuvem do Azure; para serviços de aplicativos, consulte [isto](../app-service/app-service-web-tutorial-custom-ssl.md).
>

Essa tarefa usa uma implantação de produção. Informações sobre como usar uma implantação de preparo são fornecidas no final deste tópico.

Leia [isso](cloud-services-how-to-create-deploy-portal.md) primeiro se você ainda não tiver criado um serviço de nuvem.

## <a name="step-1-get-an-ssl-certificate"></a>Passo 1: Obter um certificado SSL
Para configurar o SSL para um aplicativo, primeiro você precisa obter um certificado SSL assinado por uma autoridade de certificação (CA), um terceiro confiável que emite certificados para essa finalidade. Se você ainda não tiver um, precisará obter um de uma empresa que vende certificados SSL.

O certificado deve atender aos seguintes requisitos para certificados SSL no Azure:

* O certificado deve conter uma chave privada.
* O certificado deve ser criado para troca de chaves, exportável para um arquivo de troca de informações pessoais (. pfx).
* O nome da entidade do certificado deve corresponder ao domínio usado para acessar o serviço de nuvem. Não é possível obter um certificado SSL de uma autoridade de certificação (CA) para o domínio cloudapp.net. Você deve adquirir um nome de domínio personalizado para usar ao acessar seu serviço. Quando você solicita um certificado de uma autoridade de certificação, o nome da entidade do certificado deve corresponder ao nome de domínio personalizado usado para acessar seu aplicativo. Por exemplo, se o nome de domínio personalizado for **contoso.com** , você solicitará um certificado de sua autoridade de certificação para * **. contoso.com** ou **www\.contoso.com**.
* O certificado deve usar um mínimo de criptografia de 2048 bits.

Para fins de teste, você pode [criar](cloud-services-certs-create.md) e usar um certificado autoassinado. Um certificado autoassinado não é autenticado por meio de uma AC e pode usar o domínio cloudapp.net como a URL do site. Por exemplo, a tarefa a seguir usa um certificado autoassinado no qual o nome comum (CN) usado no certificado é **sslexample.cloudapp.net**.

Em seguida, você deve incluir informações sobre o certificado em sua definição de serviço e arquivos de configuração de serviço.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Passo 2: Modificar os arquivos de configuração e definição de serviço
Seu aplicativo deve ser configurado para usar o certificado e um ponto de extremidade HTTPS deve ser adicionado. Como resultado, os arquivos de definição de serviço e configuração de serviço precisam ser atualizados.

1. No ambiente de desenvolvimento, abra o arquivo de definição de serviço (CSDEF), adicione uma seção **certificados** na seção WebRole e inclua as seguintes informações sobre o certificado (e os certificados intermediários):

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

   A seção **certificados** define o nome do nosso certificado, sua localização e o nome da loja onde ele está localizado.

   As permissões`permissionLevel` (atributo) podem ser definidas como um dos seguintes valores:

   | Valor de permissão | Descrição |
   | --- | --- |
   | limitedOrElevated |**(Padrão)** Todos os processos de função podem acessar a chave privada. |
   | com privilégios elevados |Somente processos elevados podem acessar a chave privada. |

2. No arquivo de definição de serviço, adicione um elemento **InputEndpoint** dentro da seção **pontos de extremidade** para habilitar https:

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

3. No arquivo de definição de serviço, adicione um elemento de **Associação** dentro da seção **sites** . Esse elemento adiciona uma associação HTTPS para mapear o ponto de extremidade para seu site:

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

   Todas as alterações necessárias no arquivo de definição de serviço foram concluídas; Mas, você ainda precisa adicionar as informações do certificado ao arquivo de configuração de serviço.
4. Em seu arquivo de configuração de serviço (CSCFG), Service Configuration. Cloud. CSCFG, adicione um valor de **certificados** com o do seu certificado. O exemplo de código a seguir fornece detalhes da seção de **certificados** , exceto para o valor de impressão digital.

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

(Este exemplo usa **SHA1** para o algoritmo de impressão digital. Especifique o valor apropriado para o algoritmo de impressão digital do certificado.)

Agora que os arquivos de definição de serviço e configuração de serviço foram atualizados, empacote a implantação para carregar no Azure. Se você estiver usando **CSPack**, não use o sinalizador **/generateConfigurationFile** , pois isso substituirá as informações de certificado que você acabou de inserir.

## <a name="step-3-upload-a-certificate"></a>Passo 3: Carregar um certificado
Conectar-se ao portal do Azure e...

1. Na seção **todos os recursos** do portal, selecione seu serviço de nuvem.

    ![Publicar seu serviço de nuvem](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Clique em **certificados**.

    ![Clique no ícone certificados](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Clique em **carregar** na parte superior da área certificados.

    ![Clique no item de menu carregar](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Forneça o **arquivo**, a **senha**e clique em **carregar** na parte inferior da área de entrada de dados.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Passo 4: Conectar-se à instância de função usando HTTPS
Agora que sua implantação está em execução no Azure, você pode se conectar a ela usando HTTPS.

1. Clique na **URL do site** para abrir o navegador da Web.

   ![Clique na URL do site](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. No navegador da Web, modifique o link para usar **https** em vez de **http**e, em seguida, visite a página.

   > [!NOTE]
   > Se você estiver usando um certificado autoassinado, ao navegar até um ponto de extremidade HTTPS associado ao certificado autoassinado, você poderá ver um erro de certificado no navegador. O uso de um certificado assinado por uma autoridade de certificação confiável elimina esse problema; Enquanto isso, você pode ignorar o erro. (Outra opção é adicionar o certificado autoassinado ao repositório de certificados da autoridade de certificação confiável do usuário.)
   >
   >

   ![Visualização do site](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Se você quiser usar SSL para uma implantação de preparo em vez de uma implantação de produção, primeiro será necessário determinar a URL usada para a implantação de preparo. Depois que o serviço de nuvem tiver sido implantado, a URL para o ambiente de preparo será determinada pelo GUID da **ID de implantação** neste formato:`https://deployment-id.cloudapp.net/`  
   >
   > Crie um certificado com o nome comum (CN) igual à URL baseada em GUID (por exemplo, **328187776e774ceda8fc57609d404462.cloudapp.net**). Use o portal para adicionar o certificado ao serviço de nuvem em etapas. Em seguida, adicione as informações do certificado aos seus arquivos CSDEF e CSCFG, reempacote seu aplicativo e atualize sua implantação em etapas para usar o novo pacote.
   >

## <a name="next-steps"></a>Passos seguintes
* [Configuração geral do seu serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Saiba como [implantar um serviço de nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configure um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerencie seu serviço de nuvem](cloud-services-how-to-manage-portal.md).
