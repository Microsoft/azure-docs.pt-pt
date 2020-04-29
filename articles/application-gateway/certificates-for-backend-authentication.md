---
title: Certificados necessários para permitir servidores de backend
titleSuffix: Azure Application Gateway
description: Este artigo fornece exemplos de como um certificado TLS/SSL pode ser convertido em certificado de autenticação e certificado de raiz fidedigno que são necessários para permitir instâncias de backend no Gateway de Aplicação Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 20f588639c54b0a8b7cd304f33b5a9d633a73be6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80133052"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Criar certificados para permitir o backend com o Portal de Aplicação Azure

Para terminar até ao fim do TLS, o Gateway de aplicação exige que as instâncias de backend sejam permitidas através do upload de certificados de autenticação/raiz fidedignos. Para o V1 SKU, são necessários certificados de autenticação, mas para os certificados de raiz fidedignos v2 SKU são necessários para permitir os certificados.

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> - Certificado de autenticação de exportação de um certificado de backend (v1 SKU)
> - Certificado de raiz fidedigno de exportação de um certificado de backend (v2 SKU)

## <a name="prerequisites"></a>Pré-requisitos

É necessário um certificado de backend existente para gerar os certificados de autenticação ou certificados de raiz fidedignos necessários para permitir instâncias de backend com o Application Gateway. O certificado de backend pode ser o mesmo que o certificado TLS/SSL ou diferente para uma maior segurança. O Gateway de aplicação não lhe fornece qualquer mecanismo para criar ou adquirir um certificado TLS/SSL. Para efeitos de teste, pode criar um certificado auto-assinado, mas não deve usá-lo para cargas de trabalho de produção. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Certificado de autenticação de exportação (v1 SKU)

É necessário um certificado de autenticação para permitir instâncias de backend no Application Gateway v1 SKU. O certificado de autenticação é a chave pública dos certificados de servidor backend na Base-64 codificada X.509(. Formato CER). Neste exemplo, utilizará um certificado TLS/SSL para o certificado de backend e exportará a sua chave pública para ser usado como certificação de autenticação. Além disso, neste exemplo, utilizará a ferramenta Do Gestor de Certificados do Windows para exportar os certificados necessários. Pode optar por utilizar qualquer outra ferramenta que seja conveniente.

A partir do seu certificado TLS/SSL, exporte o ficheiro chave pública .cer (não a chave privada). Os seguintes passos ajudam a exportar o ficheiro .cer na Base-64 codificada X.509(. Formato CER) para o seu certificado:

1. Para obter um ficheiro .cer a partir do certificado, abra **Gerir certificados de utilizador**. Localize o certificado, tipicamente em 'Certificados - Utilizador Atual\Certificados Pessoais\', e clique à direita. Clique em **Todas as Tarefas** e, em seguida, clique em **Exportar**. Esta ação abre o **Assistente para Exportar Certificados**. Se não encontrar o certificado nos certificados de utilizador'pessoal\certificados, pode ter acidentalmente aberto "Certificados - Computador Local", em vez de "Certificados - Utilizador Atual"). Se pretender abrir o Certificate Manager no âmbito atual do utilizador utilizando o PowerShell, escreva *certmgr* na janela da consola.

   ![Exportar](./media/certificates-for-backend-authentication/export.png)

2. No Assistente, clique em **Seguinte**.

   ![Exportar o certificado](./media/certificates-for-backend-authentication/exportwizard.png)

3. Selecione **Não, não exportar a chave privada** e, em seguida, clique em **Seguinte**.

   ![Não exporte a chave privada](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Na página **Formato do Ficheiro de Exportação**, selecione **X.509 codificado com Base-64 (.CER).** e, em seguida, clique em **Seguinte**.

   ![Base-64 codificada](./media/certificates-for-backend-authentication/base64.png)

5. Para **File to Export**, **Navegue** no local para onde pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Em seguida, clique **em Next**.

   ![Procurar](./media/certificates-for-backend-authentication/browse.png)

6. Clique em **Concluir** para exportar o certificado.

   ![Concluir](./media/certificates-for-backend-authentication/finish.png)

7. O seu certificado é exportado com sucesso.

   ![Êxito](./media/certificates-for-backend-authentication/success.png)

   O certificado exportado é semelhante ao seguinte:

   ![Exportado](./media/certificates-for-backend-authentication/exported.png)

8. Se abrir o certificado exportado utilizando o Bloco de Notas, verá algo semelhante a este exemplo. A secção em azul contém a informação que é enviada para o gateway da aplicação. Se abrir o certificado com o Bloco de Notas e não se parecer com este, normalmente isto significa que não o exportou usando o X.509 codificado base-64(. Formato CER). Além disso, se quiser usar um editor de texto diferente, entenda que alguns editores podem introduzir formatação não intencional em segundo plano. Isto pode criar problemas quando enviado o texto deste certificado para o Azure.

   ![Aberto com Bloco de Notas](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Certificado de raiz fidedigno de exportação (v2 SKU)

O certificado de raiz fidedigno é necessário para whitelist backend instâncias no gateway de aplicação v2 SKU. O certificado de raiz é um Base-64 codificado X.509(. Cer) certificado raiz de formato dos certificados de servidor de backend. Neste exemplo, utilizaremos um certificado TLS/SSL para o certificado de backend, exportaremos a sua chave pública e, em seguida, exportaremos o certificado de raiz da chave de confiança da chave pública no formato codificado base64 para obter o certificado raiz fidedigno. Os certificados intermédios devem ser agregados com o certificado do servidor e instalados no servidor de backend.

Os seguintes passos ajudam-no a exportar o ficheiro .cer para o seu certificado:

1. Utilize as etapas 1-9 mencionadas na secção **Certificado de autenticação exportação de um certificado de backend (v1 SKU)** acima para exportar a chave pública do seu certificado de backend.

2. Uma vez exportada a chave pública, abra o ficheiro.

   ![Certificado de autorização aberta](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![sobre certificado](./media/certificates-for-backend-authentication/general.png)

3. Passe para a vista Caminho de Certificação para ver a autoridade de certificação.

   ![detalhes cert](./media/certificates-for-backend-authentication/certdetails.png)

4. Selecione o certificado raiz e clique no **Certificado 'Ver'.**

   ![caminho cert](./media/certificates-for-backend-authentication/rootcert.png)

   Devia ver os detalhes do certificado de raiz.

   ![informação cert](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Mova-se para a vista **Detalhes** e clique em **Copiar para Arquivar...**

   ![cópia cert raiz](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. Neste momento, extraiu os detalhes do certificado de raiz do certificado de backend. Verá o Assistente de **Exportação**de Certificados. Utilize agora as etapas 2-9 mencionadas na secção **Certificado de autenticação exportação de um certificado de backend (v1 SKU)** acima para exportar o certificado de raiz fidedigno na Base-64 codificada X.509(. Formato CER).

## <a name="next-steps"></a>Passos seguintes

Agora tem o certificado de autenticação/certificado raiz fidedigno na Base-64 codificado X.509(. Formato CER). Pode adicionar isto à porta de entrada da aplicação para whitelist os seus servidores backend para terminar a encriptação TLS. Consulte [a configuração final para terminar o TLS utilizando o Gateway da Aplicação com powerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).

