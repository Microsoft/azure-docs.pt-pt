---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d16412e4e35714c840516670f520f77daed1676d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80059906"
---
Quando gera um certificado de cliente, é instalado automaticamente no computador que usou para o gerar. Se pretender instalar o certificado de cliente noutro computador cliente, tem de exportar o certificado de cliente que gerou.

1. Para exportar um certificado de cliente, abra **Gerir Certificados de Utilizador**. Os certificados de cliente que gerou estão, por defeito, localizados em 'Certificados - Utilizador Atual\Personal\Certificates'. Clique com o botão direito no certificado de cliente que pretende exportar, clique em **todas as tarefas**e, em seguida, clique em **Exportar** para abrir o **Assistente de Exportação de Certificados**.

   ![Exportar](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. No Assistente de Exportação de Certificados, clique **em Seguinte** para continuar.

   ![Seguinte](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Selecione **Sim, exporte a chave privada**e, em seguida, clique em **Seguinte**.

   ![exportar chave privada](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Na página **Exportar Formato de Ficheiro**, deixe as predefinições selecionadas. Certifique-se de que **Incluir todos os certificados no caminho de certificação, se possível** está selecionado. Esta definição exporta ainda as informações do certificado de raiz necessárias para a autenticação bem sucedida do cliente. Sem isso, a autenticação do cliente falha porque o cliente não tem o certificado de raiz fidedigno. Em seguida, clique **em Seguinte**.

   ![formato de arquivo de exportação](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Na página **Segurança**, tem de proteger a chave privada. Se optar por utilizar uma palavra-passe, certifique-se de que regista ou memoriza a palavra-passe que define para este certificado. Em seguida, clique **em Seguinte**.

   ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. Em **Ficheiro a Exportar**, **Navegue** até à localização para a qual pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Em seguida, clique **em Seguinte**.

   ![arquivo para exportar](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Clique em **Concluir** para exportar o certificado.

   ![concluir](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)