---
title: 'VPN Gateway: Cliente VPN de resolução de problemas - autenticação AZure AD'
description: Clientes de autenticação AD VPN Gateway P2S Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 56a8514fc2531ba0b18925427814e5bfef7d64bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84988101"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Resolução de problemas de um cliente VPN de autenticação AZure AD

Este artigo ajuda-o a resolver problemas com um cliente VPN para se conectar a uma rede virtual utilizando a autenticação point-to-site VPN e Azure Ative Directory.

## <a name="view-status-log"></a><a name="status"></a>Ver Registo de Estado

Consulte o registo de estado de mensagens de erro.

![registos](./media/troubleshoot-ad-vpn-client/1.png)

1. Clique no ícone setas no canto inferior direito da janela do cliente para mostrar os **Registos de Estado**.
2. Verifique se os registos podem indicar o problema.
3. As mensagens de erro são apresentadas a vermelho.

## <a name="clear-sign-in-information"></a><a name="clear"></a>Informação clara de inscrição

Limpe a informação de inscrição.

![iniciar sessão](./media/troubleshoot-ad-vpn-client/2.png)

1. Selecione o ... ao lado do perfil que quer resolver problemas. Selecione **Configurar -> Conta Econom:**
2. Selecione **Guardar**.
3. Tente ligar-se.
4. Se a ligação continuar a falhar, continue para a secção seguinte.

## <a name="run-diagnostics"></a><a name="diagnostics"></a>Executar diagnósticos

Executar diagnósticos no cliente VPN.

![diagnósticos](./media/troubleshoot-ad-vpn-client/3.png)

1. Clique no **...** ao lado do perfil em que pretende fazer diagnósticos. Selecione **diagnóstico -> Diagnóstico de Execução**.
2. O cliente irá executar uma série de testes e exibir o resultado do teste

   * Acesso à Internet – Verifica se o cliente tem conectividade com a Internet
   * Credenciais de Cliente – Verifique se o ponto final de autenticação do Azure Ative Directory está acessível
   * Servidor Resolve- Contacta o servidor DNS para resolver o endereço IP do servidor VPN configurado
   * Servidor Reachable – Verifica se o servidor VPN está a responder ou não
3. Se algum dos testes falhar, contacte o administrador da rede para resolver o problema.
4. A próxima secção mostra-lhe como recolher os registos, se necessário.

## <a name="collect-client-log-files"></a><a name="logfiles"></a>Recolher ficheiros de registo de clientes

Recolha os ficheiros de registo de clientes VPN. Os ficheiros de registo podem ser enviados para suporte/administrador através de um método à sua escolha. Por exemplo, e-mail.

1. Clique no "..." ao lado do perfil em que pretende fazer diagnósticos. Selecione **diagnosticar -> mostrar diretório**.

   ![mostrar troncos](./media/troubleshoot-ad-vpn-client/4.png)
2. O Windows Explorer abre-se para a pasta que contém os ficheiros de registo.

   ![ver arquivo](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [Criar um inquilino do Azure Ative Directory para ligações P2S Open VPN que utilizam a autenticação AZURE AD](openvpn-azure-ad-tenant.md).