---
title: RADIUS e servidor MFA do Azure-Azure Active Directory
description: A implementar a autenticação RADIUS e Servidor Multi-Factor Authentication do Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b38341613c98bf85df8cb47ccafc3df5709a1fd4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425208"
---
# <a name="integrate-radius-authentication-with-azure-multi-factor-authentication-server"></a>Integrar a autenticação RADIUS com o Servidor Multi-Factor Authentication do Azure

RADIUS é um protocolo padrão para aceitar pedidos de autenticação e processar esses pedidos. O Servidor Multi-Factor Authentication do Azure pode funcionar como um servidor RADIUS. Insira-o entre o cliente RADIUS (a aplicação VPN) e o destino de autenticação para adicionar a verificação de dois passos. O destino de autenticação pode ser o Active Directory, um diretório LDAP ou outro servidor RADIUS. Para o Multi-Factor Authentication (MFA) do Azure funcionar, tem de configurar o Servidor MFA do Azure para poder comunicar com os servidores cliente e o destino de autenticação. O Servidor MFA do Azure aceita os pedidos de um cliente RADIUS, valida as credenciais no destino de autenticação, adiciona o Multi-Factor Authentication do Azure e envia uma resposta novamente para o cliente RADIUS. O pedido de autenticação só é bem sucedido se a autenticação primária e o Multi-Factor Authentication do Azure forem bem sucedidos.

> [!IMPORTANT]
> Este artigo é apenas para usuários do servidor MFA do Azure. Se você usar a MFA do Azure baseada em nuvem, veja como [integrar com a autenticação RADIUS para o Azure MFA](howto-mfa-nps-extension.md).
>
> A partir de 1º de julho de 2019, a Microsoft não oferecerá mais o servidor MFA para novas implantações. Novos clientes que queiram exigir a autenticação multifator de seus usuários devem usar a autenticação multifator do Azure baseada em nuvem. Os clientes existentes que ativaram o servidor MFA antes de 1º de julho poderão baixar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

> [!NOTE]
> O Servidor MFA apenas suporta os protocolos RADIUS PAP (protocolo de autenticação de palavras-passe) e MSCHAPv2 (Challenge Handshake Authentication Protocol da Microsoft) ao atuar como um servidor RADIUS.  Podem ser utilizados outros protocolos, como o EAP (protocolo de autenticação extensível), quando o servidor MFA funciona como um proxy RADIUS para outro servidor RADIUS que suporte esse protocolo.
>
> Nesta configuração, os tokens SMS e OATH unidirecionais não funcionam, uma vez que o Servidor MFA não consegue iniciar uma resposta Challenge RADIUS com êxito utilizando protocolos alternativos.

![Autenticação RADIUS no servidor MFA](./media/howto-mfaserver-dir-radius/radius.png)

## <a name="add-a-radius-client"></a>Adicionar um cliente RADIUS

Para configurar a autenticação RADIUS, instale o Servidor Multi-Factor Authentication do Azure num servidor do Windows. Se tiver um ambiente do Active Directory, o servidor deve ser associado ao domínio dentro da rede. Utilize o seguinte procedimento para configurar o Servidor Multi-Factor Authentication do Azure:

1. No Servidor Multi-Factor Authentication do Azure, clique no ícone Autenticação RADIUS no menu da esquerda.
2. Marque a caixa de verificação **Ativar autenticação RADIUS**.
3. No separador Clientes, altere a portas de Autenticação e Gestão de Contas se o serviço RADIUS do MFA do Azure tiver de escutar os pedidos RADIUS em portas não padrão.
4. Clique em **Adicionar**.
5. Introduza o endereço IP da aplicação/servidor que irá autenticar no Servidor Multi-Factor Authentication do Azure, um nome de aplicação (opcional) e um segredo partilhado.

   O nome da aplicação aparece nos relatórios e poderá ser apresentado nas mensagens de autenticação SMS ou da aplicação móvel.

   O segredo partilhado tem de ser o mesmo no Servidor Multi-Factor Authentication do Azure e na aplicação/servidor.

6. Marque a caixa **Exigir correspondência de utilizador Multi-Factor Authentication** se todos os utilizadores tiverem sido importados para o Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de utilizadores ainda não tiverem sido importados para o Servidor ou são excluídos da verificação em dois passos, deixe a caixa desmarcada.
7. Marque a caixa **Ativar token OATH de contingência** se pretender utilizar códigos de acesso a partir de aplicações móveis de verificação como método de cópia de segurança.
8. Clique em **OK**.

Repita os passos de 4 a 8 para adicionar o número de clientes RADIUS adicionais que precisar.

## <a name="configure-your-radius-client"></a>Configurar o cliente RADIUS

1. Clique no separador **Destino**.
   * Se o servidor MFA do Azure estiver instalado em um servidor ingressado em domínio em um ambiente Active Directory, selecione **domínio do Windows**.
   * Se os utilizadores tiverem de ser autenticados num diretório LDAP, selecione **Enlace de LDAP**.
      Selecione o ícone de Integração de Diretórios e edite a configuração LDAP no separador Definições, para que o Servidor possa ser vinculado ao seu diretório. Pode encontrar instruções para a configuração de LDAP no [guia de configuração do Proxy LDAP](howto-mfaserver-dir-ldap.md).
   * Se os usuários devem ser autenticados em outro servidor RADIUS, selecione **servidor (es) RADIUS**.
1. Clique em **Adicionar** para configurar o servidor no qual o servidor MFA do Azure fará o proxy dos pedidos RADIUS.
1. Na caixa de diálogo Adicionar Servidor RADIUS, introduza o endereço IP do servidor RADIUS e um segredo partilhado.

   O segredo partilhado tem de ser o mesmo no Servidor Multi-Factor Authentication do Azure e no servidor RADIUS. Altere a porta de Autenticação e a porta de Gestão de Contas se forem utilizadas portas diferentes pelo servidor RADIUS.

1. Clique em **OK**.
1. Adicione o Servidor MFA do Azure como um cliente RADIUS no outro servidor RADIUS para que possa processar os pedidos de acesso enviados do Servidor MFA do Azure. Utilize o mesmo segredo partilhado configurado no Servidor Multi-Factor Authentication do Azure.

Repita estes passos para adicionar mais servidores RADIUS. Configure a ordem pela qual o Servidor MFA do Azure os deve chamar com os botões **Mover Para Cima** e **Mover Para Baixo**.

Configurou com êxito o Servidor Multi-Factor Authentication do Azure. O Servidor está agora a escutar nas portas configuradas para pedidos de acesso RADIUS dos clientes configurados.

## <a name="radius-client-configuration"></a>Configuração do Cliente RADIUS

Para configurar o cliente RADIUS, utilize as diretrizes:

* Configure a sua aplicação/servidor para autenticar através de RADIUS no endereço IP do Servidor Multi-Factor Authentication do Azure, que funciona como servidor RADIUS.
* Utilize o mesmo segredo partilhado que foi configurado anteriormente.
* Configure o tempo limite de RADIUS para 30 a 60 segundos, de modo a que exista tempo suficiente para validar as credenciais do utilizador, executar a verificação de dois passos, receber a respetiva resposta e, em seguida, responder ao pedido de acesso RADIUS.

## <a name="next-steps"></a>Passos seguintes

Saiba como [integrar com a autenticação RADIUS](howto-mfa-nps-extension.md) se tiver o Azure Multi-factor Authentication na cloud. 
