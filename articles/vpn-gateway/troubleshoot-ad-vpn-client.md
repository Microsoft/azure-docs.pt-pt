---
title: 'Gateway de VPN: solucionar problemas de cliente VPN-autenticação do Azure AD'
description: Solucionar problemas do gateway de VPN P2S clientes de autenticação do Azure AD
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 8871e92f0911c4d3cbcc1772bef1daeb5c70b5d7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151967"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Solucionar problemas de um cliente VPN de autenticação do Azure AD

Este artigo ajuda a solucionar problemas de um cliente VPN para se conectar a uma rede virtual usando VPN ponto a site e autenticação Azure Active Directory.

## <a name="status"></a>Exibir log de status

Exibir o log de status para mensagens de erro.

![registos](./media/troubleshoot-ad-vpn-client/1.png)

1. Clique no ícone de setas no canto inferior direito da janela do cliente para mostrar os **logs de status**.
2. Verifique os logs em busca de erros que possam indicar o problema.
3. As mensagens de erro são exibidas em vermelho.

## <a name="clear"></a>Limpar informações de entrada

Limpe as informações de entrada.

![iniciar sessão](./media/troubleshoot-ad-vpn-client/2.png)

1. Selecione o... ao lado do perfil que você deseja solucionar problemas. Selecione **Configurar-> limpar conta salva**.
2. Selecione **Guardar**.
3. Tente ligar-se.
4. Se a conexão ainda falhar, prossiga para a próxima seção.

## <a name="diagnostics"></a>Executar diagnóstico

Execute o diagnóstico no cliente VPN.

![diagnósticos](./media/troubleshoot-ad-vpn-client/3.png)

1. Clique em **...** ao lado do perfil no qual você deseja executar o diagnóstico. Selecione **diagnosticar-> executar diagnóstico**.
2. O cliente executará uma série de testes e exibirá o resultado do teste

   * Acesso à Internet – verifica se o cliente tem conectividade com a Internet
   * Credenciais do cliente – Verifique se o ponto de extremidade de autenticação do Azure Active Directory está acessível
   * Servidor resolvível – entra em contato com o servidor DNS para resolver o endereço IP do servidor VPN configurado
   * Servidor alcançável – verifica se o servidor VPN está respondendo ou não
3. Se algum dos testes falhar, contate o administrador da rede para resolver o problema.
4. A próxima seção mostra como coletar os logs, se necessário.

## <a name="logfiles"></a>Coletar arquivos de log do cliente

Colete os arquivos de log do cliente VPN. Os arquivos de log podem ser enviados para suporte/administrador por meio de um método de sua escolha. Por exemplo, email.

1. Clique no botão "..." ao lado do perfil no qual você deseja executar o diagnóstico. Selecione **diagnosticar-> mostrar diretório de logs**.

   ![Mostrar logs](./media/troubleshoot-ad-vpn-client/4.png)
2. O Windows Explorer é aberto na pasta que contém os arquivos de log.

   ![Exibir arquivo](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [criar um locatário de Azure Active Directory para conexões VPN abertas do P2S que usam a autenticação do Azure ad](openvpn-azure-ad-tenant.md).