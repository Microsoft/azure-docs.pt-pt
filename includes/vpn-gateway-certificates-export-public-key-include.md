---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d2dba0f657b418267db90c07014dc8996ed12a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059948"
---
Depois de criar um certificado de raiz auto-assinado, exporte o certificado de raiz do ficheiro público .cer (não a chave privada). Mais tarde, enviará este ficheiro para o Azure. Os seguintes passos ajudam-no a exportar o ficheiro .cer para o seu certificado raiz auto-assinado:

1. Para obter um ficheiro .cer a partir do certificado, abra **Gerir certificados de utilizador**. Localize o certificado de raiz autoassinado, normalmente em ''Certificates - Current User\Personal\Certificates'', e clique com o botão direito do rato. Clique em **Todas as Tarefas** e, em seguida, clique em **Exportar**. Esta ação abre o **Assistente para Exportar Certificados**. Se não encontrar o certificado nos certificados de utilizador'pessoal\certificados, pode ter acidentalmente aberto "Certificados - Computador Local", em vez de "Certificados - Utilizador Atual"). Se pretender abrir o Certificate Manager no âmbito atual do utilizador utilizando o PowerShell, escreva *certmgr* na janela da consola.

   ![Exportar](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. No Assistente, clique em **Seguinte**.

   ![Exportar o certificado](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Selecione **Não, não exportar a chave privada** e, em seguida, clique em **Seguinte**.

   ![Não exporte a chave privada](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Na página **Formato do Ficheiro de Exportação**, selecione **X.509 codificado com Base-64 (.CER).** e, em seguida, clique em **Seguinte**.

   ![Base-64 codificada](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. Para **File to Export**, **Navegue** no local para onde pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Em seguida, clique **em Next**.

   ![Procurar](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Clique em **Concluir** para exportar o certificado.

   ![Concluir](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. O seu certificado é exportado com sucesso.

   ![Êxito](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. O certificado exportado é semelhante ao seguinte:

   ![Exportado](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Se abrir o certificado exportado utilizando o Bloco de Notas, verá algo semelhante a este exemplo. A secção em azul contém a informação que é enviada para O Azure. Se abrir o certificado com o Bloco de Notas e não se parecer com este, normalmente isto significa que não o exportou utilizando o X.509 codificado base-64(. Formato CER). Além disso, se quiser usar um editor de texto diferente, entenda que alguns editores podem introduzir formatação não intencional em segundo plano. Isto pode criar problemas quando enviado o texto deste certificado para o Azure.

   ![Aberto com Bloco de Notas](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
