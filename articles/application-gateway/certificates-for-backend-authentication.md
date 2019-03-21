---
title: Certificados necessários para o back-ends de listas de permissões no Gateway de aplicação do Azure
description: Este artigo fornece exemplos de como um certificado SSL pode ser convertido para o certificado de autenticação e o certificado de raiz fidedigna que são necessários para as instâncias de back-end de lista de permissões no Gateway de aplicação do Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/14/2019
ms.author: absha
ms.openlocfilehash: 72ee9123ad959c0c7240d4f7a906adc1a4dd1a93
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260606"
---
# <a name="create-certificates-for-whitelisting-backend-with-azure-application-gateway"></a>Criar certificados para o back-end de lista de permissões com o Gateway de aplicação do Azure

Para efetuar o SSL ponto a ponto, o gateway de aplicação requer as instâncias de back-end de permissões através do carregamento de certificados de raiz fidedigna/autenticação. Em caso de SKU do v1, os certificados de autenticação são necessários, ao passo que no caso de SKU do v2, fidedignos os certificados de raiz são necessários para a inclusão de certificados

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> - Exportar o certificado de autenticação a partir de um certificado de back-end (para o SKU de v1)
> - Exportar o certificado de raiz fidedigna a partir de um certificado de back-end (para o SKU de v2)

## <a name="prerequisites"></a>Pré-requisitos

É necessário um certificado de back-end existente para gerar os certificados de autenticação ou certificados de raiz fidedigna necessários para instâncias de back-end de lista de permissões com o gateway de aplicação. O certificado de back-end pode ser o mesmo que o certificado SSL ou diferentes para aumentar a segurança. Gateway de aplicação não fornecem qualquer mecanismo para criar ou comprar um certificado SSL. Para fins de teste, pode criar um certificado autoassinado mas não deve usá-lo para cargas de trabalho de produção. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Exportar o certificado de autenticação (para o SKU de v1)

Certificado de autenticação é necessário para instâncias de back-end de lista de permissões no v1 do gateway de aplicação SKU. Certificado de autenticação é a chave pública de certificados de servidor de back-end na Base 64 com codificação X.509 (. Formato CER). Neste exemplo, iremos utilizar um certificado SSL para o certificado de back-end e exportar a sua chave pública para ser utilizado como a certificação de autenticação. Além disso, neste exemplo, utilizamos a ferramenta de Gestor de certificados do Windows para exportar os certificados necessários. Pode optar por usar qualquer outra ferramenta de acordo com a sua comodidade.

Do seu certificado SSL, exporte o ficheiro. cer de chave pública (não a chave privada). Os passos seguintes ajuda exporta-a. cer X.509 com codificação de ficheiro em Base-64 (. Formato CER) para o seu certificado:

1. Para obter um ficheiro .cer a partir do certificado, abra **Gerir certificados de utilizador**. Localize o certificado, normalmente em 'Certificates - user\personal\certificates' atual' e, com o botão direito. Clique em **Todas as Tarefas** e, em seguida, clique em **Exportar**. Esta ação abre o **Assistente para Exportar Certificados**. Se não encontrar o certificado em User\Personal\Certificates atual, pode ter abriu acidentalmente "Certificados – Local computador", em vez de "Certificados – utilizador atual"). Se quiser abrir o Gestor de certificados no âmbito do utilizador atual com o PowerShell, que escreve *certmgr* na janela da consola.

   ![Exportar](./media/certificates-for-backend-authentication/export.png)

2. No Assistente, clique em **Seguinte**.

   ![Exportar o certificado](./media/certificates-for-backend-authentication/exportwizard.png)

3. Selecione **Não, não exportar a chave privada** e, em seguida, clique em **Seguinte**.

   ![Não exporte a chave privada](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Na página **Formato do Ficheiro de Exportação**, selecione **X.509 codificado com Base-64 (.CER).** e, em seguida, clique em **Seguinte**.

   ![Com codificação base 64](./media/certificates-for-backend-authentication/base64.png)

5. Para **ficheiro a exportar**, **procurar** para a localização para o qual pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Clique depois em **Seguinte**.

   ![Procurar](./media/certificates-for-backend-authentication/browse.png)

6. Clique em **Concluir** para exportar o certificado.

   ![Concluir](./media/certificates-for-backend-authentication/finish.png)

7. O certificado é exportado com êxito.

   ![Êxito](./media/certificates-for-backend-authentication/success.png)

   O certificado exportado será semelhante ao seguinte:

   ![Exportado](./media/certificates-for-backend-authentication/exported.png)

8. Se abrir o certificado exportado usando o bloco de notas, verá algo semelhante a este exemplo. A seção em azul contém as informações que são carregadas para o gateway de aplicação. Se abrir o certificado com o bloco de notas e não ser semelhante ao seguinte, normalmente, isso significa que não exportá-lo utilizando a Base 64 com codificação X.509 (. Formato CER). Além disso, se pretender utilizar um editor de texto diferente, compreenda que podem apresentar alguns editores de formatação não-intencionais em segundo plano. Isto pode provocar problemas quando carregado o texto deste certificado para o Azure.

   ![Abrir com o bloco de notas](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Exportar o certificado de raiz fidedigna (para o SKU de v2)

Fidedigno o certificado de raiz é necessário para instâncias de back-end de lista de permissões no v2 do gateway de aplicação SKU. O certificado de raiz é X.509 com codificação de Base-64 (. Certificado de raiz do formato CER) de certificados de servidor de back-end. Neste exemplo, iremos utilizar um certificado SSL para o certificado de back-end, exportar a sua chave pública e, em seguida, exporte o certificado de raiz da AC fidedigna a partir da chave pública no formato codificado em base64 para obter o certificado de raiz fidedigna. 

Os passos seguintes ajudam-na exportar o ficheiro. cer para o seu certificado:

1. Utilize os passos 1 a 9, mencionado na secção **exportar o certificado de autenticação de um certificado de back-end (para a v1 SKU)** acima para exportar a chave pública do seu certificado de back-end.

2. Depois da chave pública tenha sido exportada, abra o ficheiro.

   ![Certificado de autorização aberto](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![sobre o certificado](./media/certificates-for-backend-authentication/general.png)

3. Mova para o caminho de certificação vista para visualizar a autoridade de certificação.

   ![Detalhes do certificado](./media/certificates-for-backend-authentication/certdetails.png)

4. Selecione o certificado de raiz e clique em **Ver certificado**.

   ![caminho do certificado](./media/certificates-for-backend-authentication/rootcert.png)

   Deverá conseguir ver os detalhes do certificado de raiz.

   ![informações de certificado](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Mover para o **detalhes** visualizar e clique em **copiar para ficheiro...**

   ![certificado de raiz de cópia](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. Neste momento, extrair os detalhes do certificado de raiz do certificado de back-end. Verá a **Assistente para exportar certificados**. Agora, utilize os passos 2 a 9, mencionado na secção **exportar o certificado de autenticação de um certificado de back-end (para a v1 SKU)** acima para a raiz fidedigna de exportar certificado na Base 64 com codificação X.509 (. Formato CER).

## <a name="next-steps"></a>Passos Seguintes

Agora tem a autenticação de certificado/considerado fidedigno o certificado de raiz na Base 64 codificada X.509 (. Formato CER). Pode adicionar isso ao gateway de aplicação à lista de permissões os servidores de back-end para encriptação SSL de ponta a ponta. Ver [como configurar a encriptação SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).