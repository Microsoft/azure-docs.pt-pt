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
ms.openlocfilehash: d16412e4e35714c840516670f520f77daed1676d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80059906"
---
Quando gera um certificado de cliente, é automaticamente instalado no computador que usou para o gerar. Se quiser instalar o certificado de cliente noutro computador cliente, tem de exportar o certificado de cliente que gerou.

1. Para exportar um certificado de cliente, abra **Gerir Certificados de Utilizador**. Os certificados de cliente que gerou estão, por defeito, localizados em 'Certificados - Utilizador Atual\Certificados Pessoais\'. Clique no certificado de cliente que pretende exportar, clique em **todas as tarefas,** e clique em **Exportar** para abrir o Certificado **De Exportador**.

   ![Exportar](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. No Certificado De Saque De Exportação, clique em **Seguir** para continuar.

   ![Seguinte](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Selecione **Sim, exporte a chave privada**e, em seguida, clique **em Next**.

   ![chave privada de exportação](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Na página **Exportar Formato de Ficheiro**, deixe as predefinições selecionadas. Certifique-se de que **Incluir todos os certificados no caminho de certificação, se possível** está selecionado. Esta definição exporta adicionalmente a informação do certificado de raiz que é necessária para a autenticação bem sucedida do cliente. Sem isso, a autenticação do cliente falha porque o cliente não tem o certificado raiz de confiança. Em seguida, clique **em Next**.

   ![formato de ficheiro de exportação](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Na página **Segurança**, tem de proteger a chave privada. Se optar por utilizar uma palavra-passe, certifique-se de que regista ou memoriza a palavra-passe que define para este certificado. Em seguida, clique **em Next**.

   ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. Em **Ficheiro a Exportar**, **Navegue** até à localização para a qual pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Em seguida, clique **em Next**.

   ![arquivo para exportar](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Clique em **Concluir** para exportar o certificado.

   ![concluir](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)