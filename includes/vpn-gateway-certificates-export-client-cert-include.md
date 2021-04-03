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
ms.openlocfilehash: 018cf621c65e86877a76c9861c999caf67f3b8cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94553217"
---
Quando gera um certificado de cliente, é instalado automaticamente no computador que usou para o gerar. Se pretender instalar o certificado de cliente noutro computador cliente, tem de exportar o certificado de cliente que gerou.

1. Para exportar um certificado de cliente, abra **Gerir Certificados de Utilizador**. Os certificados de cliente que gerou estão, por defeito, localizados em 'Certificados - Utilizador Atual\Personal\Certificates'. Clique com o botão direito no certificado de cliente que pretende exportar, clique em **todas as tarefas** e, em seguida, clique em **Exportar** para abrir o **Assistente de Exportação de Certificados**.

   ![A screenshot mostra a janela certificados para o utilizador atual com certificados selecionados e Exportação selecionados de todas as tarefas.](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. No Assistente de Exportação de Certificados, clique **em Seguinte** para continuar.

   ![A screenshot mostra a mensagem de boas-vindas do assistente de exportação de certificados.](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Selecione **Sim, exporte a chave privada** e, em seguida, clique em **Seguinte**.

   ![exportar chave privada](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Na página **Exportar Formato de Ficheiro**, deixe as predefinições selecionadas. Certifique-se de que **Incluir todos os certificados no caminho de certificação, se possível** está selecionado. Esta definição exporta ainda as informações do certificado de raiz necessárias para a autenticação bem sucedida do cliente. Sem isso, a autenticação do cliente falha porque o cliente não tem o certificado de raiz fidedigno. Em seguida, clique **em Seguinte**.

   ![formato de arquivo de exportação](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Na página **Segurança**, tem de proteger a chave privada. Se optar por utilizar uma palavra-passe, certifique-se de que regista ou memoriza a palavra-passe que define para este certificado. Em seguida, clique **em Seguinte**.

   ![A screenshot mostra a página de Segurança do Assistente de Exportação de Certificados com a palavra-passe inserida e confirmada e seguinte em destaque.](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. Em **Ficheiro a Exportar**, **Navegue** até à localização para a qual pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Em seguida, clique **em Seguinte**.

   ![arquivo para exportar](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Clique em **Concluir** para exportar o certificado.

   ![A screenshot mostra o Assistente de Exportação de Certificados com as definições inseridas.](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)