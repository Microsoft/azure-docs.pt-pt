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
ms.openlocfilehash: 12e9bec0c560f1b068b07a1b6afe218a112e439f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94553172"
---
Depois de criar um certificado de raiz auto-assinado, exporte o certificado de raiz .cer ficheiro (não a chave privada). Mais tarde, irá enviar este ficheiro para o Azure. Os seguintes passos ajudam-no a exportar o ficheiro .cer para o seu certificado de raiz auto-assinado:

1. Para obter um ficheiro .cer a partir do certificado, abra **Gerir certificados de utilizador**. Localize o certificado de raiz autoassinado, normalmente em ''Certificates - Current User\Personal\Certificates'', e clique com o botão direito do rato. Clique em **Todas as Tarefas** e, em seguida, clique em **Exportar**. Esta ação abre o **Assistente para Exportar Certificados**. Se não conseguir encontrar o certificado nos termos do Utilizador Atual\Personal\Certificates, poderá ter aberto acidentalmente "Certificados - Computador Local", em vez de "Certificados - Utilizador Atual"). Se pretender abrir o Certificate Manager no âmbito atual do utilizador utilizando o PowerShell, digite *certmgr* na janela da consola.

   ![O Screenshot mostra a janela certificados para o utilizador atual com certificados selecionados e um menu contextual com Exportação selecionado de Todas as Tarefas.](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. No Assistente, clique em **Seguinte**.

   ![Exportar o certificado](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Selecione **Não, não exportar a chave privada** e, em seguida, clique em **Seguinte**.

   ![Não exporte a chave privada](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Na página **Formato do Ficheiro de Exportação**, selecione **X.509 codificado com Base-64 (.CER).** e, em seguida, clique em **Seguinte**.

   ![Base-64 codificada](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. Para **arquivar para exportar,** **navegue** para o local para onde pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Em seguida, clique **em Seguinte**.

   ![A screenshot mostra o Assistente de Exportação de Certificados com uma caixa de texto com nome de ficheiro e uma opção Browse.](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Clique em **Concluir** para exportar o certificado.

   ![A screenshot mostra o Assistente de Exportação de Certificados com as definições selecionadas.](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. O seu certificado é exportado com sucesso.

   ![A screenshot mostra uma mensagem de que a exportação foi bem sucedida.](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. O certificado exportado é semelhante a este:

   ![A screenshot mostra um ícone de certificado e nome de ficheiro com a extensão do nome do ficheiro c e r.](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Se abrir o certificado exportado usando o Bloco de Notas, vê algo semelhante a este exemplo. A secção azul contém a informação que é enviada para Azure. Se abrir o certificado com o Notepad e não se parecer com este, normalmente isto significa que não o exportou utilizando o X.509 codificado base-64(. Formato CER). Além disso, se quiser usar um editor de texto diferente, entenda que alguns editores podem introduzir formatação não intencional em segundo plano. Isto pode criar problemas quando enviado o texto deste certificado para Azure.

   ![Aberto com Bloco de Notas](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
