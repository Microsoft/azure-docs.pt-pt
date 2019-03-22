---
title: Configurar SSL para um serviço em nuvem | Documentos da Microsoft
description: Saiba como especificar um ponto final HTTPS para uma função web e como carregar um certificado SSL para proteger a sua aplicação. Estes exemplos utilizam o portal do Azure.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 371ba204-48b6-41af-ab9f-ed1d64efe704
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: jeconnoc
ms.openlocfilehash: 2a9879ebc55a5f25c1a358e386697dce1c55ec90
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58084349"
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Configurar o SSL para uma aplicação no Azure

A encriptação Secure Socket Layer (SSL) é o método mais utilizado para proteger dados enviados através da Internet. Esta tarefa comum debate como especificar um ponto final HTTPS para uma função Web e como carregar um certificado SSL para proteger a sua aplicação.

> [!NOTE]
> Os procedimentos nesta tarefa, aplicam-se nos serviços de Cloud do Azure; para serviços de aplicações, consulte [isso](../app-service/app-service-web-tutorial-custom-ssl.md).
>

Esta tarefa utiliza uma implementação de produção. Informações sobre como utilizar uma implementação de teste são fornecidas no final deste tópico.

Leia [isso](cloud-services-how-to-create-deploy-portal.md) primeiro se ainda não tiver criado um serviço em nuvem.

## <a name="step-1-get-an-ssl-certificate"></a>Passo 1: Obter um certificado SSL
Para configurar SSL para uma aplicação, tem primeiro de obter um certificado SSL assinado por um certificado de autoridade (AC), um terceiro confiável que emite certificados para esta finalidade. Se ainda não tiver um, precisa de obter uma de uma empresa que vende certificados SSL.

O certificado tem de cumprir os seguintes requisitos para certificados SSL no Azure:

* O certificado tem de conter uma chave privada.
* O certificado tem de ser criado para troca de chaves, exportável para um ficheiro Personal Information Exchange (. pfx).
* Nome do requerente do certificado tem de corresponder o domínio utilizado para aceder ao serviço cloud. Não é possível obter um certificado SSL de uma autoridade de certificação (AC) para o domínio cloudapp.net. Tem de adquirir um nome de domínio personalizado para utilizar quando aceder ao seu serviço. Quando pedir um certificado a partir de uma AC, nome do requerente do certificado tem de corresponder ao nome de domínio personalizado utilizado para aceder à sua aplicação. Por exemplo, se o nome de domínio personalizado for **contoso.com** poderia solicitar um certificado da AC para ***. contoso.com** ou **www\.contoso.com**.
* O certificado tem de utilizar um mínimo de encriptação de 2048 bits.

Para fins de teste, pode [criar](cloud-services-certs-create.md) e utilizar um certificado autoassinado. Um certificado autoassinado não é autenticado por meio de uma autoridade de certificação e pode utilizar o domínio cloudapp.net, como o URL do Web site. Por exemplo, a seguinte tarefa utiliza um certificado autoassinado em que é o nome comum (CN) utilizado no certificado **sslexample.cloudapp.net**.

Em seguida, tem de incluir informações sobre o certificado na sua definição de serviço e os ficheiros de configuração do serviço.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Passo 2: Modificar os ficheiros de configuração e definição de serviço
A aplicação tem de ser configurada para utilizar o certificado e um ponto final HTTPS tem de ser adicionado. Como resultado, a definição de serviço e os ficheiros de configuração de serviço tem de ser atualizado.

1. No seu ambiente de desenvolvimento, abra o ficheiro de definição de serviço (. CSDEF), adicione uma **certificados** secção dentro do **WebRole** secção e inclua as seguintes informações sobre o certificado (e certificados intermédios):

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

   O **certificados** secção define o nome do nosso certificado, a localização e o nome do arquivo de onde está localizado.

   Permissões (`permissionLevel` atributo) pode ser definido como um dos seguintes valores:

   | Valor de permissão | Descrição |
   | --- | --- |
   | limitedOrElevated |**(Predefinição)**  Todos os processos de função podem aceder à chave privada. |
   | elevada |Apenas os processos elevados, podem aceder à chave privada. |

2. No seu ficheiro de definição de serviço, adicione uma **ao InputEndpoint** elemento dentro do **pontos finais** secção para ativar o HTTPS:

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

3. No seu ficheiro de definição de serviço, adicione uma **enlace** elemento dentro os **Sites** secção. Este elemento adiciona um enlace de HTTPS para mapear o ponto de extremidade para seu site:

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

   Concluídas todas as alterações necessárias para o ficheiro de definição de serviço; No entanto, ainda precisa de adicionar as informações do certificado para o ficheiro de configuração do serviço.
4. No serviço ficheiro de configuração (CSCFG), Serviceconfiguration, adicione uma **certificados** valor com que o certificado. O código de exemplo seguinte fornece detalhes sobre o **certificados** secção, exceto o valor do thumbprint.

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

(Este exemplo utiliza **sha1** para o algoritmo de thumbprint. Especifique o valor apropriado para o algoritmo de thumbprint do certificado.)

Agora que a definição de serviço e os ficheiros de configuração do serviço terem sido atualizados, Empacote a sua implementação para carregar para o Azure. Se estiver a utilizar **cspack**, não utilize a **/generateConfigurationFile** sinalizar, à medida que irá substituir as informações do certificado que acaba de ser inserido.

## <a name="step-3-upload-a-certificate"></a>Passo 3: Carregar um certificado
Ligar ao portal do Azure e...

1. Na **todos os recursos** seção do Portal, selecione o seu serviço cloud.

    ![Publicar o seu serviço cloud](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Clique em **certificados**.

    ![Clique no ícone de certificados](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Clique em **carregar** na parte superior da área de certificados.

    ![Clique no item de menu de carregamento](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Forneça o **arquivo**, **palavra-passe**, em seguida, clique em **carregar** na parte inferior da área de entrada de dados.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Passo 4: Ligar à instância de função através de HTTPS
Agora que sua implementação está em execução no Azure, pode ligar ao mesmo através de HTTPS.

1. Clique nas **URL do Site** para abrir o navegador da web.

   ![Clique no URL do Site](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. No seu navegador da web, modifique a ligação para utilizar **https** em vez de **http**e, em seguida, visite a página.

   > [!NOTE]
   > Se estiver a utilizar um certificado autoassinado, ao navegar para um ponto final HTTPS associada ao certificado autoassinado, que poderá ver um erro de certificado no browser. Utilizar um certificado assinado por uma autoridade de certificação fidedigna elimina esse problema; Entretanto, pode ignorar o erro. (Outra opção é adicionar o certificado autoassinado ao arquivo de certificados de autoridade de certificado fidedigno do usuário.)
   >
   >

   ![Pré-visualização do site](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Se pretender utilizar o SSL para uma implementação de teste, em vez de uma implementação de produção, primeiro terá de determinar o URL utilizado para a implementação de teste. Depois de ter sido implementado o seu serviço cloud, o URL para o ambiente de teste é determinado pelos **ID de implementação** GUID neste formato: `https://deployment-id.cloudapp.net/`  
   >
   > Criar um certificado com o nome comum (CN) igual para o URL com base no GUID (por exemplo, **328187776e774ceda8fc57609d404462.cloudapp.net**). Utilize o portal para adicionar o certificado ao seu serviço cloud em etapas. Em seguida, adicione as informações do certificado aos seus ficheiros CSDEF e CSCFG, reempacotar o aplicativo e atualizar a implementação faseada para utilizar o novo pacote.
   >

## <a name="next-steps"></a>Passos Seguintes
* [Configuração geral do seu serviço cloud](cloud-services-how-to-configure-portal.md).
* Saiba como [implementar um serviço cloud](cloud-services-how-to-create-deploy-portal.md).
* Configurar uma [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerir o seu serviço cloud](cloud-services-how-to-manage-portal.md).
