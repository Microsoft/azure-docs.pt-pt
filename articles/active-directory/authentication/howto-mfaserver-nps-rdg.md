---
title: RDG e servidor MFA do Azure usando RADIUS-Azure Active Directory
description: Esta é a página do Multi-Factor Authentication do Azure que irá ajudar a implementar o Gateway de Ambiente de Trabalho Remoto (RD) e o Servidor Multi-Factor Authentication do Azure com o RADIUS.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ef90ce9e6d3849a4c778326b02040f0b1fc764a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848022"
---
# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>Gateway de Ambiente de Trabalho Remoto e Servidor Multi-Factor Authentication do Azure com o RADIUS

Geralmente, o gateway de Área de Trabalho Remota (RD) usa os [serviços de diretiva de rede (NPS)](https://docs.microsoft.com/windows-server/networking/core-network-guide/core-network-guide#BKMK_optionalfeatures) locais para autenticar usuários. Este artigo descreve como encaminhar um pedido RADIUS fora do Gateway de Ambiente de Trabalho Remoto (através do NPS local) para o Servidor Multi-Factor Authentication. A combinação do MFA do Azure e o Gateway de RD significa que os utilizadores podem a aceder aos respetivos ambientes de trabalho a partir de qualquer lugar, executando a autenticação incontestável.

Uma vez que a Autenticação do Windows para os serviços de terminal não é suportada para o Server 2012 R2, utilize o Gateway de RD e RADIUS para integrar com o Servidor de MFA.

Instale o Servidor Multi-Factor Authentication num servidor separado, o qual irá utilizar o proxy no pedido RADIUS de volta para o NPS no Servidor de Gateway de Ambiente de Trabalho Remoto. Após o NPS validar o nome de utilizador e a palavra-passe, ele devolve uma resposta ao Servidor Multi-Factor Authentication. Em seguida, o Servidor MFA realiza o segundo fator de autenticação e devolve um resultado ao gateway.

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferecerá mais o servidor MFA para novas implantações. Novos clientes que queiram exigir a autenticação multifator de seus usuários devem usar a autenticação multifator do Azure baseada em nuvem. Os clientes existentes que ativaram o servidor MFA antes de 1º de julho poderão baixar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

## <a name="prerequisites"></a>Pré-requisitos

- Um Servidor de MFA do Azure associado a um domínio. Se ainda não tiver um instalado, siga os passos em [Getting started with the Azure Multi-Factor Authentication Server (Introdução ao Servidor Multi-Factor Authentication do Azure)](howto-mfaserver-deploy.md).
- Um servidor NPS configurado existente.
- Um Gateway de Ambiente de Trabalho Remoto que autentica com os Serviços de Política de Rede.

> [!NOTE]
> Este artigo deve ser usado somente com implantações de servidor MFA, não com o Azure MFA (baseado em nuvem).

## <a name="configure-the-remote-desktop-gateway"></a>Configurar o Gateway de Ambiente de Trabalho Remoto

Configure o Gateway de RD para enviar a autenticação RADIUS para um Servidor Multi-Factor Authentication do Azure.

1. No Gestor de Gateway de RD, clique com o botão direito do rato no nome do servidor e selecione **Propriedades**.
2. Aceda ao separador **Arquivo CAP de RD** e selecione **Servidor central a executar o NPS**.
3. Adicione um ou mais Servidores Multi-Factor Authentication do Azure como servidores RADIUS ao introduzir o nome ou o endereço IP de cada servidor.
4. Crie um segredo partilhado para cada servidor.

## <a name="configure-nps"></a>Configurar o NPS

O Gateway de RD utiliza o NPS para enviar o pedido RADIUS para o Multi-Factor Authentication do Azure. Para configurar o NPS, primeiro altera as definições do tempo limite para impedir que o Gateway de RD exceda o tempo limite, antes da verificação de dois passos ser concluída. Em seguida, atualize o NPS para receber as autenticações RADIUS do seu Servidor de MFA. Utilize o procedimento seguinte para configurar o NPS:

### <a name="modify-the-timeout-policy"></a>Modificar a política de tempo limite

1. No NPS, abra o menu **Clientes e Servidor RADIUS** na coluna esquerda e selecione em **Grupos de Servidores RADIUS Remotos**.
2. Selecione o **GRUPO DE SERVIDOR DE GATEWAY DE TS**.
3. Aceda ao separador **Balanceamento de Carga**.
4. Altere o **Número de segundos sem resposta antes de o pedido ser cancelado** e o **Número de segundos entre pedidos quando o servidor é identificado como não disponível** para entre 30 a 60 segundos. (Se achar que o servidor continua a exceder o tempo limite durante a autenticação, pode voltar aqui e aumentar o número de segundos.)
5. Aceda ao separador **Autenticação/Conta** e certifique-se de que as portas RADIUS especificadas correspondem às portas em que o Servidor Multi-Factor Authentication está a escutar.

### <a name="prepare-nps-to-receive-authentications-from-the-mfa-server"></a>Preparar o NPS para receber as autenticações a partir do Servidor de MFA

1. Clique com o botão direito do rato em **Clientes RADIUS** em Clientes RADIUS e Servidores na coluna esquerda e selecione **Novo**.
2. Adicione o Servidor Multi-Factor Authentication do Azure como cliente RADIUS. Escolha um nome amigável e especifique um segredo partilhado.
3. Abra o menu **Políticas** na coluna esquerda e selecione **Políticas de Pedido de Ligação**. Deve ver uma política denominada POLÍTICA DE AUTORIZAÇÃO DO GATEWAY TS que foi criada quando o Gateway de RD foi configurado. Esta política encaminha os pedidos RADIUS para o Servidor Multi-Factor Authentication.
4. Clique com o botão direito do rato em **POLÍTICA DE AUTORIZAÇÃO DE GATEWAY TS** e selecione **Duplicar Política**.
5. Abra a nova política e aceda ao separador **Condições**.
6. Adicione uma condição que corresponda ao Nome Amigável do Cliente com o nome amigável definido no passo 2 para o cliente RADIUS do Servidor Multi-Factor Authentication do Azure.
7. Aceda ao separador **Definições** e selecione **Autenticação**.
8. Altere o Fornecedor de Autenticação para **Autenticar pedidos neste servidor**. Esta política assegura que, quando o NPS recebe um pedido RADIUS do Servidor MFA do Azure, a autenticação ocorre localmente em vez de enviar um pedido RADIUS novamente para o Servidor Multi-Factor Authentication do Azure, o que poderia resultar numa condição de ciclo.
9. Para impedir uma condição de ciclo, certifique-se de que a nova política é pedida ACIMA da política original no painel **Políticas de Pedido de Ligação**.

## <a name="configure-azure-multi-factor-authentication"></a>Configurar o Multi-Factor Authentication do Azure

O Servidor Multi-Factor Authentication do Azure está configurado como um proxy RADIUS entre o Gateway de RD e o NPS.  Deve ser instalado num servidor associado a um domínio separado do servidor do Gateway de RD. Utilize o seguinte procedimento para configurar o Servidor Multi-Factor Authentication do Azure.

1. Abra o Servidor Multi-Factor Authentication do Azure e selecione no ícone Autenticação RADIUS.
2. Marque a caixa de verificação **Ativar autenticação RADIUS**.
3. No separador Clientes, certifique-se de que as portas correspondem ao que está configurado no NPS e, em seguida, selecione **Adicionar**.
4. Adicione o endereço IP do servidor do Gateway de RD, o nome da aplicação (opcional) e um segredo partilhado. O segredo partilhado tem de ser o mesmo no Servidor Multi-Factor Authentication do Azure e no Gateway de RD.
3. Aceda ao separador **Destino** e selecione o botão de rádio **Servidor(es) RADIUS**.
4. Selecione **Adicionar** e introduza o segredo partilhado do endereço IP e as portas do servidor NPS. A menos que esteja a utilizar um NPS central, o cliente RADIUS e o destino RADIUS são os mesmos. O segredo partilhado tem de corresponder a uma configuração na secção Cliente RADIUS do servidor NPS.

![Autenticação RADIUS no servidor MFA](./media/howto-mfaserver-nps-rdg/radius.png)

## <a name="next-steps"></a>Passos seguintes

- Integre a MFA do Azure e [aplicações Web do IIS](howto-mfaserver-iis.md)

- Obtenha as respostas na [FAQ sobre o Multi-Factor Authentication do Azure](multi-factor-authentication-faq.md)
