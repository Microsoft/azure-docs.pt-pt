---
title: Exportação de cadeia de certificados CA de cliente fidedigno para autenticação de clientes
titleSuffix: Azure Application Gateway
description: Saiba como exportar uma cadeia de certificados ca de confiança para autenticação de clientes no Azure Application Gateway
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2021
ms.author: caya
ms.openlocfilehash: 2329dc7426b223ef2c81dd0e2e607bccf73192e6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231555"
---
# <a name="export-a-trusted-client-ca-certificate-chain-to-use-with-client-authentication"></a>Exportar uma cadeia de certificados CA de cliente confiável para usar com autenticação do cliente
Para configurar a autenticação mútua com o cliente, ou autenticação do cliente, a Application Gateway requer que uma cadeia de certificados CA de cliente confiável seja carregada para o gateway. Se tiver várias cadeias de certificados, terá de criar as correntes separadamente e carregá-las como diferentes ficheiros no Gateway de Aplicações. Neste artigo, você vai aprender a exportar uma cadeia de certificados CA de cliente confiável que você pode usar na configuração de autenticação do seu cliente no seu gateway.  

## <a name="prerequisites"></a>Pré-requisitos

Um certificado de cliente existente é necessário para gerar a cadeia de certificados ca cliente fidedigna. 

## <a name="export-trusted-client-ca-certificate"></a>Certificado ca cliente fidedigno de exportação

O certificado CA do cliente fidedigno é necessário para permitir a autenticação do cliente no Gateway de Aplicação. Neste exemplo, usaremos um certificado TLS/SSL para o certificado de cliente, exportaremos a sua chave pública e exportaremos os certificados de CA da chave pública para obter os certificados de CA de cliente fidedigno. Em seguida, vamos concatenar todos os certificados de CA do cliente em uma cadeia de certificados de ca cliente confiável. 

Os seguintes passos ajudam-no a exportar o ficheiro .pem ou .cer para o seu certificado:

### <a name="export-public-certificate"></a>Certificado público de exportação 

1. Para obter um ficheiro .cer a partir do certificado, abra **Gerir certificados de utilizador**. Localize o certificado, normalmente em 'Certificados - Utilizador atual\Personal\Certificates', e clique à direita. Clique em **Todas as Tarefas** e, em seguida, clique em **Exportar**. Esta ação abre o **Assistente para Exportar Certificados**. Se não conseguir encontrar o certificado nos termos do Utilizador Atual\Personal\Certificates, poderá ter aberto acidentalmente "Certificados - Computador Local", em vez de "Certificados - Utilizador Atual"). Se pretender abrir o Certificate Manager no âmbito atual do utilizador utilizando o PowerShell, digite *certmgr* na janela da consola.

    > [!div class="mx-imgBorder"]
    > ![O Screenshot mostra o Gestor de Certificados com Certificados selecionados e um menu contextual com todas as tarefas, em seguida, Exportar selecionado.](./media/certificates-for-backend-authentication/export.png)

2. No Assistente, clique em **Seguinte**.
    > [!div class="mx-imgBorder"]
    > ![Exportar o certificado](./media/certificates-for-backend-authentication/exportwizard.png)

3. Selecione **Não, não exportar a chave privada** e, em seguida, clique em **Seguinte**.
    > [!div class="mx-imgBorder"]
    > ![Não exporte a chave privada](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Na página **Formato do Ficheiro de Exportação**, selecione **X.509 codificado com Base-64 (.CER).** e, em seguida, clique em **Seguinte**.
    > [!div class="mx-imgBorder"]
    > ![Base-64 codificada](./media/certificates-for-backend-authentication/base64.png)

5. Para **arquivar para exportar,** **navegue** para o local para onde pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Em seguida, clique **em Seguinte**.

    > [!div class="mx-imgBorder"]
   > ![A screenshot mostra o Assistente de Exportação de Certificados onde especifica um ficheiro para exportar.](./media/certificates-for-backend-authentication/browse.png)

6. Clique em **Concluir** para exportar o certificado.

    > [!div class="mx-imgBorder"]
    > ![A screenshot mostra o Assistente de Exportação de Certificados depois de completar a exportação de ficheiros.](./media/certificates-for-backend-authentication/finish.png)

7. O seu certificado é exportado com sucesso.

    > [!div class="mx-imgBorder"]
    > ![A screenshot mostra o Assistente de Exportação de Certificados com uma mensagem de sucesso.](./media/certificates-for-backend-authentication/success.png)

   O certificado exportado é semelhante a este:

    > [!div class="mx-imgBorder"]
    > ![A imagem mostra um símbolo de certificado.](./media/certificates-for-backend-authentication/exported.png)

### <a name="export-ca-certificates-from-the-public-certificate"></a>Certificados de A.c. de exportação a partir do certificado público

Agora que exportou o seu certificado público, irá agora exportar o certificado(s) de AC a partir do seu certificado público. Se tiver apenas um CA de raiz, só precisará exportar esse certificado. No entanto, se tiver mais de 1+ CAs intermédios, também terá de exportar cada um deles. 

1. Uma vez exportada a chave pública, abra o ficheiro.

    > [!div class="mx-imgBorder"]
    > ![Certificado de autorização aberta](./media/certificates-for-backend-authentication/openAuthcert.png)

    > [!div class="mx-imgBorder"]
    > ![sobre certificado](./media/mutual-authentication-certificate-management/general.png)

1. Selecione o separador Caminho de Certificação para ver a autoridade de certificação.

    > [!div class="mx-imgBorder"]
    > ![Detalhes do cert](./media/mutual-authentication-certificate-management/cert-details.png) 

1. Selecione o certificado de raiz e clique no **Certificado de Visualização**.

    > [!div class="mx-imgBorder"]
    > ![caminho cert](./media/mutual-authentication-certificate-management/root-cert.png) 

   Devia ver os detalhes do certificado de raiz.

    > [!div class="mx-imgBorder"]
    > ![informação cert](./media/mutual-authentication-certificate-management/root-cert-details.png)

1. Selecione o separador **Detalhes** e clique em **Copiar para Arquivar...**

    > [!div class="mx-imgBorder"]
    > ![cert raiz de cópia](./media/mutual-authentication-certificate-management/root-cert-copy-to-file.png)

1. Neste momento, extraiu os detalhes do certificado de aptidão raiz do certificado público. Verá o **Assistente de Exportação de Certificados.** Siga os passos 2-7 da secção anterior ([Certificado público de exportação)](./mutual-authentication-certificate-management.md#export-public-certificate)para completar o Assistente de Exportação de Certificados. 

1. Repita agora os passos 2-6 desta secção atual ([certificados(s) de exportação do certificado público](./mutual-authentication-certificate-management.md#export-ca-certificates-from-the-public-certificate)) para todos os CAs intermédios para exportar todos os certificados de CA intermédios na Base-64 codificada X.509(. Formato CER).

    > [!div class="mx-imgBorder"]
    > ![cert intermédio](./media/mutual-authentication-certificate-management/intermediate-cert.png)

    Por exemplo, repetiria os passos 2-6 desta secção na CA intermediária *MSIT CAZ2* para extraí-lo como certificado próprio. 

### <a name="concatenate-all-your-ca-certificates-into-one-file"></a>Concatenate todos os seus certificados de CA em um ficheiro

1. Executar o seguinte comando com todos os certificados de AC que extraiu anteriormente. 

    Windows:
    ```console
    type intermediateCA.cer rootCA.cer > combined.cer
    ```
    
    Linux:
    ```console
    cat intermediateCA.cer rootCA.cer >> combined.cer
    ```

    O seu certificado combinado resultante deve ser semelhante ao seguinte:
    
    > [!div class="mx-imgBorder"]
    > ![cert combinado](./media/mutual-authentication-certificate-management/combined-cert.png)

## <a name="next-steps"></a>Passos seguintes

Agora tem a cadeia de certificados ca cliente de confiança. Pode adicionar isto à configuração de autenticação do seu cliente no Gateway de aplicações para permitir a autenticação mútua com o seu gateway. Consulte [a autenticação mútua de configuração utilizando o Portal da Aplicação com portal](./mutual-authentication-portal.md) ou [configuure a autenticação mútua utilizando o Gateway de Aplicação com o PowerShell.](./mutual-authentication-powershell.md)

