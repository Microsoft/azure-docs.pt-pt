---
title: Certificados necessários para permitir servidores de backend
titleSuffix: Azure Application Gateway
description: Este artigo fornece exemplos de como um certificado TLS/SSL pode ser convertido em certificado de autenticação e certificado de raiz fidedigno que são necessários para permitir instâncias de backend em Gateway de aplicação Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/17/2020
ms.author: absha
ms.openlocfilehash: 874e554063f64ddefce99a223678d64b2e0774c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397727"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Criar certificados para permitir o back-end com o Gateway de Aplicação do Azure

Para terminar o TLS, o Application Gateway exige que as instâncias de backend sejam permitidas através do upload de certificados de autenticação/raiz fidedigno. Para o V1 SKU, são necessários certificados de autenticação, mas para os certificados de raiz fidedignos v2 SKU são necessários para permitir os certificados.

Neste artigo, vai aprender a:


- Certificado de autenticação de exportação a partir de um certificado de backend (para v1 SKU)
- Certificado de raiz fidedigno de exportação a partir de um certificado de backend (para v2 SKU)

## <a name="prerequisites"></a>Pré-requisitos

Um certificado de backend existente é necessário para gerar os certificados de autenticação ou certificados de raiz fidedignos necessários para permitir casos de backend com o Gateway de aplicação. O certificado de backend pode ser o mesmo que o certificado TLS/SSL ou diferente para uma segurança adicional. O Application Gateway não lhe fornece qualquer mecanismo para criar ou adquirir um certificado TLS/SSL. Para efeitos de teste, pode criar um certificado auto-assinado, mas não deve usá-lo para cargas de trabalho de produção. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Certificado de autenticação de exportação (para v1 SKU)

É necessário um certificado de autenticação para permitir casos de backend no Application Gateway v1 SKU. O certificado de autenticação é a chave pública dos certificados de servidor backend na Base-64 codificada X.509(. Formato CER). Neste exemplo, utilizará um certificado TLS/SSL para o certificado de backend e exportará a sua chave pública para ser usada como certificação de autenticação. Além disso, neste exemplo, utilizará a ferramenta Windows Certificate Manager para exportar os certificados necessários. Pode optar por utilizar qualquer outra ferramenta que seja conveniente.

A partir do seu certificado TLS/SSL, exporte o ficheiro de .cer chave pública (não a chave privada). Os seguintes passos ajudam-no a exportar o ficheiro .cer na Base-64 codificada X.509(. CER) formato para o seu certificado:

1. Para obter um ficheiro .cer a partir do certificado, abra **Gerir certificados de utilizador**. Localize o certificado, normalmente em 'Certificados - Utilizador atual\Personal\Certificates', e clique à direita. Clique em **Todas as Tarefas** e, em seguida, clique em **Exportar**. Esta ação abre o **Assistente para Exportar Certificados**. Se não conseguir encontrar o certificado nos termos do Utilizador Atual\Personal\Certificates, poderá ter aberto acidentalmente "Certificados - Computador Local", em vez de "Certificados - Utilizador Atual"). Se pretender abrir o Certificate Manager no âmbito atual do utilizador utilizando o PowerShell, digite *certmgr* na janela da consola.

   ![O Screenshot mostra o Gestor de Certificados com Certificados selecionados e um menu contextual com todas as tarefas, em seguida, Exportar selecionado.](./media/certificates-for-backend-authentication/export.png)

2. No Assistente, clique em **Seguinte**.

   ![Exportar o certificado](./media/certificates-for-backend-authentication/exportwizard.png)

3. Selecione **Não, não exportar a chave privada** e, em seguida, clique em **Seguinte**.

   ![Não exporte a chave privada](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Na página **Formato do Ficheiro de Exportação**, selecione **X.509 codificado com Base-64 (.CER).** e, em seguida, clique em **Seguinte**.

   ![Base-64 codificada](./media/certificates-for-backend-authentication/base64.png)

5. Para **arquivar para exportar,** **navegue** para o local para onde pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Em seguida, clique **em Seguinte**.

   ![A screenshot mostra o Assistente de Exportação de Certificados onde especifica um ficheiro para exportar.](./media/certificates-for-backend-authentication/browse.png)

6. Clique em **Concluir** para exportar o certificado.

   ![A screenshot mostra o Assistente de Exportação de Certificados depois de completar a exportação de ficheiros.](./media/certificates-for-backend-authentication/finish.png)

7. O seu certificado é exportado com sucesso.

   ![A screenshot mostra o Assistente de Exportação de Certificados com uma mensagem de sucesso.](./media/certificates-for-backend-authentication/success.png)

   O certificado exportado é semelhante a este:

   ![A imagem mostra um símbolo de certificado.](./media/certificates-for-backend-authentication/exported.png)

8. Se abrir o certificado exportado usando o Bloco de Notas, vê algo semelhante a este exemplo. A secção azul contém a informação que é enviada para o gateway de aplicações. Se abrir o certificado com o Notepad e não se parecer com este, normalmente isto significa que não o exportou usando o X.509 codificado base-64(. Formato CER). Além disso, se quiser usar um editor de texto diferente, entenda que alguns editores podem introduzir formatação não intencional em segundo plano. Isto pode criar problemas quando enviado o texto deste certificado para Azure.

   ![Aberto com Bloco de Notas](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Certificado de raiz fidedigno de exportação (para v2 SKU)

O certificado de raiz fidedigno é necessário para permitir casos de backend no gateway de aplicação v2 SKU. O certificado de raiz é um X.509 codificado base-64(. CER) certificado de raiz de formato a partir dos certificados de servidor de backend. Neste exemplo, usaremos um certificado TLS/SSL para o certificado de backend, exportaremos a sua chave pública e exportaremos o certificado de raiz da CA fidedigna a partir da chave pública em formato codificado base64 para obter o certificado raiz fidedigno. Os certificados intermédios devem ser agregados com o certificado do servidor e instalados no servidor backend.

Os seguintes passos ajudam-no a exportar o ficheiro .cer para o seu certificado:

1. Utilize os passos 1 - 8 mencionados na secção anterior [Certificado de autenticação de exportação (para v1 SKU)](#export-authentication-certificate-for-v1-sku) para exportar a chave pública do seu certificado de backend.

2. Uma vez exportada a chave pública, abra o ficheiro.

   ![Certificado de autorização aberta](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![sobre certificado](./media/certificates-for-backend-authentication/general.png)

3. Mude para a vista caminho de certificação para ver a autoridade de certificação.

   ![Detalhes do cert](./media/certificates-for-backend-authentication/certdetails.png)

4. Selecione o certificado de raiz e clique no **Certificado de Visualização**.

   ![caminho cert](./media/certificates-for-backend-authentication/rootcert.png)

   Devia ver os detalhes do certificado de raiz.

   ![informação cert](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Mover para a vista **detalhes** e clicar **em Copiar para Arquivar...**

   ![cert raiz de cópia](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. Neste momento, extraiu os detalhes do certificado de raiz do certificado de backend. Verá o **Assistente de Exportação de Certificados.** Utilize agora os passos 2-9 mencionados na secção **Certificado de autenticação de exportação a partir de um certificado de backend (para v1 SKU)** acima para exportar o certificado de raiz fidedigno no X.509 codificado base-64(. Formato CER).

## <a name="next-steps"></a>Passos seguintes

Agora tem o certificado de autenticação/certificado de raiz fidedigno na Base-64 codificada X.509(. Formato CER). Pode adicionar isto ao gateway da aplicação para permitir que os seus servidores de backend acabem com a encriptação TLS. Consulte [o fim da configuração para terminar o TLS utilizando o Gateway de Aplicação com PowerShell](./application-gateway-end-to-end-ssl-powershell.md).