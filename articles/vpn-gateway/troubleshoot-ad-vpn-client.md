---
title: 'VPN Gateway: Troubleshoot VPN cliente - Autenticação Azure AD'
description: Clientes de autenticação aditiva VPN Gateway P2S Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 8871e92f0911c4d3cbcc1772bef1daeb5c70b5d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74151967"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Problemas de resolução de um cliente VPN de autenticação AD Azure

Este artigo ajuda-o a resolver problemas com um cliente VPN para se conectar a uma rede virtual utilizando a autenticação de Diretório Sondar-a-site e Azure Ative Directory.

## <a name="view-status-log"></a><a name="status"></a>Ver Registo de Estado

Consulte o registo de estado para obter mensagens de erro.

![registos](./media/troubleshoot-ad-vpn-client/1.png)

1. Clique no ícone das setas no canto inferior direito da janela do cliente para mostrar os **Registos de Estado**.
2. Verifique se há erros que possam indicar o problema.
3. As mensagens de erro são exibidas a vermelho.

## <a name="clear-sign-in-information"></a><a name="clear"></a>Informação clara de inscrição

Limpe a informação de inscrição.

![iniciar sessão](./media/troubleshoot-ad-vpn-client/2.png)

1. Selecione o ... ao lado do perfil que quer resolver. **Selecione Configur -> Clear Saved Account**.
2. Selecione **Guardar**.
3. Tente ligar-se.
4. Se a ligação ainda falhar, continue para a secção seguinte.

## <a name="run-diagnostics"></a><a name="diagnostics"></a>Executar diagnósticos

Execute diagnósticos no cliente VPN.

![diagnósticos](./media/troubleshoot-ad-vpn-client/3.png)

1. Clique no **...** ao lado do perfil em que pretende executar diagnósticos. **Selecione Diagnóstico de Diagnóstico -> Executar**.
2. O cliente executará uma série de testes e exibirá o resultado do teste

   * Acesso à Internet – Verifica para ver se o cliente tem conectividade com a Internet
   * Credenciais de cliente – Verifique se o ponto final de autenticação do Diretório Ativo Azure é acessível
   * Resolver o servidor do servidor Servidor DNS para resolver o endereço IP do servidor VPN configurado
   * Alcance do servidor - Verifica se o servidor VPN está a responder ou não
3. Se algum dos testes falhar, contacte o administrador da rede para resolver o problema.
4. A secção seguinte mostra-lhe como recolher os registos, se necessário.

## <a name="collect-client-log-files"></a><a name="logfiles"></a>Recolher ficheiros de registo de clientes

Recolha os ficheiros de registo do cliente VPN. Os ficheiros de registo podem ser enviados para suporte/administrador através de um método à sua escolha. Por exemplo, e-mail.

1. Clique no "..." ao lado do perfil em que pretende executar diagnósticos. **Selecione Diagnostic -> Mostrar Diretório de Registos**.

   ![mostrar registos](./media/troubleshoot-ad-vpn-client/4.png)
2. O Windows Explorer abre-se para a pasta que contém os ficheiros de registo.

   ![ver arquivo](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [Criar um inquilino azure ative diretório para ligações P2S Open VPN que utilizem a autenticação Azure AD](openvpn-azure-ad-tenant.md).