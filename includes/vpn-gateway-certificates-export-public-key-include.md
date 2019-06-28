---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/13/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: afd4836229c60ebef1536d4fa1ca4206a492e56d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184135"
---
Depois de criar um certificado de raiz autoassinado, exporte o ficheiro. cer de chave pública do certificado de raiz (não a chave privada). Mais tarde irá carregar o ficheiro para o Azure. Os passos seguintes ajudam-na exportar o ficheiro. cer para o seu certificado de raiz autoassinado:

1. Para obter um ficheiro .cer a partir do certificado, abra **Gerir certificados de utilizador**. Localize o certificado de raiz autoassinado, normalmente em ''Certificates - Current User\Personal\Certificates'', e clique com o botão direito do rato. Clique em **Todas as Tarefas** e, em seguida, clique em **Exportar**. Esta ação abre o **Assistente para Exportar Certificados**. Se não encontrar o certificado em User\Personal\Certificates atual, pode ter abriu acidentalmente "Certificados – Local computador", em vez de "Certificados – utilizador atual"). Se quiser abrir o Gestor de certificados no âmbito do utilizador atual com o PowerShell, que escreve *certmgr* na janela da consola.

   ![Exportar](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. No Assistente, clique em **Seguinte**.

   ![Exportar o certificado](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Selecione **Não, não exportar a chave privada** e, em seguida, clique em **Seguinte**.

   ![Não exporte a chave privada](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Na página **Formato do Ficheiro de Exportação**, selecione **X.509 codificado com Base-64 (.CER).** e, em seguida, clique em **Seguinte**.

   ![Com codificação base 64](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. Para **ficheiro a exportar**, **procurar** para a localização para o qual pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Clique depois em **Seguinte**.

   ![Procurar](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Clique em **Concluir** para exportar o certificado.

   ![Concluir](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. O certificado é exportado com êxito.

   ![Êxito](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. O certificado exportado será semelhante ao seguinte:

   ![Exportados](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Se abrir o certificado exportado usando o bloco de notas, verá algo semelhante a este exemplo. A seção em azul contém as informações que são carregadas para o Azure. Se abrir o certificado com o bloco de notas e não ser semelhante ao seguinte, normalmente, isso significa que não exportá-lo utilizando a Base 64 com codificação X.509 (. Formato CER). Além disso, se pretender utilizar um editor de texto diferente, compreenda que podem apresentar alguns editores de formatação não-intencionais em segundo plano. Isto pode provocar problemas quando carregado o texto deste certificado para o Azure.

   ![Abrir com o bloco de notas](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
