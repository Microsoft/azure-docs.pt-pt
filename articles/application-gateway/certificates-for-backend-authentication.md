---
title: Certificados necessários para back-ends de lista de permissões no gateway Aplicativo Azure
description: Este artigo fornece exemplos de como um certificado SSL pode ser convertido em um certificado de autenticação e um certificado raiz confiável que são necessários para instâncias de back-end de lista de permissões no gateway Aplicativo Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: absha
ms.openlocfilehash: ae1ac3df3da4e5c25e5538f0e8cc4cd12f9186c6
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774779"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Criar certificados para permitir o back-end com Aplicativo Azure gateway

Para fazer SSL de ponta a ponta, o gateway de aplicativo requer que as instâncias de back-end sejam permitidas carregando autenticação/certificados raiz confiáveis. Para a SKU v1, os certificados de autenticação são necessários, mas para os certificados de raiz confiáveis de SKU v2 são necessários para permitir os certificados.

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> - Exportar o certificado de autenticação de um certificado de back-end (para SKU v1)
> - Exportar certificado raiz confiável de um certificado de back-end (para SKU v2)

## <a name="prerequisites"></a>Pré-requisitos

Um certificado de back-end existente é necessário para gerar os certificados de autenticação ou certificados raiz confiáveis necessários para permitir instâncias de back-end com o gateway de aplicativo. O certificado de back-end pode ser igual ao certificado SSL ou diferente para segurança adicional. O gateway de aplicativo não fornece a você nenhum mecanismo para criar ou comprar um certificado SSL. Para fins de teste, você pode criar um certificado autoassinado, mas não deve usá-lo para cargas de trabalho de produção. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Exportar certificado de autenticação (para SKU v1)

Um certificado de autenticação é necessário para permitir instâncias de back-end no SKU do gateway de aplicativo v1. O certificado de autenticação é a chave pública de certificados de servidor de back-end em X. 509 codificado em base-64 (. CER) formato. Neste exemplo, você usará um certificado SSL para o certificado de back-end e exportará sua chave pública para ser usada como certificação de autenticação. Além disso, neste exemplo, você usará a ferramenta Gerenciador de certificados do Windows para exportar os certificados necessários. Você pode optar por usar qualquer outra ferramenta que seja conveniente.

Em seu certificado SSL, exporte o arquivo. cer de chave pública (não a chave privada). As etapas a seguir ajudam a exportar o arquivo. cer em X. 509 codificado em base-64 (. CER) formato para seu certificado:

1. Para obter um ficheiro .cer a partir do certificado, abra **Gerir certificados de utilizador**. Localize o certificado, normalmente em ' Certificates-Current User\Personal\Certificates ' e clique com o botão direito do mouse. Clique em **Todas as Tarefas** e, em seguida, clique em **Exportar**. Esta ação abre o **Assistente para Exportar Certificados**. Se você não encontrar o certificado no User\Personal\Certificates atual, poderá ter aberto acidentalmente "certificados – computador local", em vez de "certificados – usuário atual"). Se você quiser abrir o Gerenciador de certificados no escopo do usuário atual usando o PowerShell, digite *certmgr* na janela do console.

   ![Exportar](./media/certificates-for-backend-authentication/export.png)

2. No Assistente, clique em **Seguinte**.

   ![Exportar o certificado](./media/certificates-for-backend-authentication/exportwizard.png)

3. Selecione **Não, não exportar a chave privada** e, em seguida, clique em **Seguinte**.

   ![Não exportar a chave privada](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Na página **Formato do Ficheiro de Exportação**, selecione **X.509 codificado com Base-64 (.CER).** e, em seguida, clique em **Seguinte**.

   ![Base-64 codificado](./media/certificates-for-backend-authentication/base64.png)

5. Para o **arquivo a ser**exportado, **navegue** até o local para o qual você deseja exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Clique depois em **Seguinte**.

   ![Procurar](./media/certificates-for-backend-authentication/browse.png)

6. Clique em **Concluir** para exportar o certificado.

   ![Concluir](./media/certificates-for-backend-authentication/finish.png)

7. Seu certificado foi exportado com êxito.

   ![Êxito](./media/certificates-for-backend-authentication/success.png)

   O certificado exportado é semelhante a este:

   ![Exportado](./media/certificates-for-backend-authentication/exported.png)

8. Se você abrir o certificado exportado usando o bloco de notas, verá algo semelhante a este exemplo. A seção em azul contém as informações que são carregadas no gateway de aplicativo. Se você abrir seu certificado com o bloco de notas e ele não for semelhante a este, normalmente isso significa que você não o exportou usando o X. 509 codificado em base-64 (. CER) formato. Além disso, se você quiser usar um editor de texto diferente, entenda que alguns editores podem introduzir formatação não intencional em segundo plano. Isso pode criar problemas ao carregar o texto desse certificado para o Azure.

   ![Abrir com o bloco de notas](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Exportar certificado raiz confiável (para SKU v2)

O certificado raiz confiável é necessário para instâncias de back-end da lista de permissões no SKU do gateway de aplicativo v2. O certificado raiz é um X. 509 codificado em base-64 (. CER) formato de certificado raiz dos certificados do servidor de back-end. Neste exemplo, usaremos um certificado SSL para o certificado de back-end, exportar sua chave pública e, em seguida, exportar o certificado raiz da AC confiável da chave pública no formato codificado em base64 para obter o certificado raiz confiável. Os certificados intermediários devem ser agrupados com o certificado do servidor e instalados no servidor de back-end.

As etapas a seguir ajudam a exportar o arquivo. cer para seu certificado:

1. Use as etapas 1-9 mencionadas na seção **Exportar certificado de autenticação de um certificado de back-end (para SKU v1)** acima para exportar a chave pública do seu certificado de back-end.

2. Depois que a chave pública tiver sido exportada, abra o arquivo.

   ![Abrir certificado de autorização](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![sobre o certificado](./media/certificates-for-backend-authentication/general.png)

3. Vá para a exibição do caminho de certificação para exibir a autoridade de certificação.

   ![detalhes do certificado](./media/certificates-for-backend-authentication/certdetails.png)

4. Selecione o certificado raiz e clique em **Exibir certificado**.

   ![caminho do certificado](./media/certificates-for-backend-authentication/rootcert.png)

   Você deve ver os detalhes do certificado raiz.

   ![informações de certificado](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Vá para o modo de exibição de **detalhes** e clique em **copiar para arquivo...**

   ![copiar certificado raiz](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. Neste ponto, você extraiu os detalhes do certificado raiz do certificado de back-end. Você verá o **Assistente para exportação de certificados**. Agora, use as etapas 2-9 mencionadas na seção **exportar o certificado de autenticação de um certificado de back-end (para SKU v1)** acima para exportar o certificado raiz confiável no X. 509 codificado em Base-64 (. CER) formato.

## <a name="next-steps"></a>Passos Seguintes

Agora você tem o certificado de autenticação/certificado raiz confiável em X. 509 codificado em base-64 (. CER) formato. Você pode adicionar isso ao gateway de aplicativo para colocar em lista branca os servidores de back-end para criptografia SSL de ponta a ponta. Consulte [como configurar a criptografia SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).

