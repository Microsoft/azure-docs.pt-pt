---
title: Alta disponibilidade para O Servidor Azure MFA - Diretório Ativo Azure
description: Implemente várias instâncias do Servidor de Autenticação Multi-Factor Azure em configurações que ofereçam alta disponibilidade.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e7b5f6bef5358acf0709f994b85215e505fa4db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80653371"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Configure Servidor de Autenticação Multi-Factor Azure para alta disponibilidade

Para obter uma alta disponibilidade com a implementação do MFA do Servidor Azure, precisa de implementar vários servidores MFA. Esta secção fornece informações sobre um design equilibrado em carga para atingir os seus alvos de alta disponibilidade na implementação do Servidor Azure MFS.

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft deixará de oferecer o MFA Server para novas implementações. Os novos clientes que pretendam exigir a autenticação de vários fatores dos seus utilizadores devem utilizar a autenticação multi-factor Azure baseada na nuvem. Os clientes existentes que ativaram o MFA Server antes do dia 1 de julho poderão descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

## <a name="mfa-server-overview"></a>Visão geral do Servidor MFA

A arquitetura de serviço Azure MFA Server compreende vários componentes, como mostra o seguinte diagrama:

 ![Componentes de Arquitetura de Servidores MFA](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

Um Servidor MFA é um Servidor Windows que tem o software de autenticação de vários fatores Azure instalado. A instância do Servidor MFA deve ser ativada pelo Serviço MFA em Azure para funcionar. Mais de um Servidor MFA pode ser instalado no local.

O primeiro Servidor MFA instalado é o master MFA Server após a ativação pelo Serviço Azure MFA por padrão. O servidor Master MFA tem uma cópia recedível da base de dados PhoneFactor.pfdata. As instalações subsequentes de instâncias do MFA Server são conhecidas como subordinadas. Os subordinados do MFA têm uma cópia replicada apenas de leitura da base de dados PhoneFactor.pfdata. Os servidores MFA replicam informações utilizando a Chamada de Procedimento Remoto (RPC). Todos os Severs MFA devem ser unidos colectivamente ou autónomos para replicar informação.

Tanto os servidores MFA mfa como subordinados mFA comunicam com o Serviço MFA quando é necessária a autenticação de dois fatores. Por exemplo, quando um utilizador tenta aceder a uma aplicação que requer autenticação de dois fatores, o utilizador será primeiro autenticado por um fornecedor de identidade, como o Ative Directory (AD).

Após autenticação bem sucedida com AD, o Servidor MFA comunicará com o Serviço MFA. O MFA Server aguarda a notificação do Serviço MFA para permitir ou negar o acesso do utilizador à aplicação.

Se o servidor principal da MFA ficar offline, as autenticações ainda podem ser processadas, mas as operações que requerem alterações na base de dados do MFA não podem ser processadas. (Exemplos incluem: adição de utilizadores, alterações PIN self-service, alteração da informação do utilizador ou acesso ao portal do utilizador)

## <a name="deployment"></a>Implementação

Considere os seguintes pontos importantes para o equilíbrio de carga Azure MFA Server e seus componentes relacionados.

* **Utilização da norma RADIUS para alcançar uma elevada disponibilidade.** Se estiver a utilizar os Servidores Azure MFA como servidores RADIUS, pode configurar potencialmente um Servidor MFA como alvo de autenticação de RADIUS primário e outros Servidores MFA Azure como alvos de autenticação secundária. No entanto, este método para alcançar uma elevada disponibilidade pode não ser prático porque deve esperar que ocorra um período de tempo para opor quando a autenticação falhar no alvo de autenticação primária antes de poder ser autenticado contra o alvo de autenticação secundária. É mais eficiente carregar o equilíbrio do tráfego RADIUS entre o cliente RADIUS e os Servidores RADIUS (neste caso, os Servidores MFA Azure que atuam como servidores RADIUS) para que possa configurar os clientes RADIUS com um único URL que possam apontar.
* **Precisa promover manualmente subordinados mFA**. Se o servidor Master Azure MFA ficar offline, os servidores secundários do Azure MFA continuam a processar pedidos de MFA. No entanto, até que um servidor MFA principal esteja disponível, os administradores não podem adicionar utilizadores ou modificar as definições de MFA, e os utilizadores não podem fazer alterações usando o portal do utilizador. Promover um MFA subordinado ao papel principal é sempre um processo manual.
* **Separabilidade dos componentes.** O Servidor Azure MFA compreende vários componentes que podem ser instalados na mesma instância do Windows Server ou em diferentes instâncias. Estes componentes incluem o Portal do Utilizador, o Serviço Web de Aplicações Móveis e o adaptador ADFS (agente). Esta separabilidade permite utilizar o Proxy de Aplicação Web para publicar o Portal do Utilizador e o Servidor Web da Aplicação Móvel a partir da rede de perímetro. Tal configuração aumenta a segurança geral do seu design, como mostra o seguinte diagrama. O Portal do Utilizador MFA e o Servidor Web da aplicação móvel também podem ser implantados em configurações equilibradas de carga HA.

   ![Servidor MFA com uma Rede de Perímetro](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **Uma palavra-passe única (OTP) sobre SMS (aka SMS unidirecional) requer a utilização de sessões pegajosas se**o tráfego for equilibrado em carga . SMS de ida é uma opção de autenticação que faz com que o Servidor MFA envie aos utilizadores uma mensagem de texto contendo um OTP. O utilizador entra na OTP numa janela rápida para completar o desafio mFA. Se carregar o equilíbrio Azure MFA Servers, o mesmo servidor que serviu o pedido inicial de autenticação deve ser o servidor que recebe a mensagem OTP do utilizador; se outro Servidor MFA receber a resposta OTP, o desafio de autenticação falha. Para mais informações, consulte [uma palavra-passe de uma vez sobre SMS adicionada ao Servidor Azure MFA](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **As implementações equilibradas em carga do Portal do Utilizador e do Serviço Web da Aplicação Móvel requerem sessões pegajosas.** Se estiver a equilibrar a carga do Portal de Utilizador do MFA e do Serviço Web da Aplicação Móvel, cada sessão tem de permanecer no mesmo servidor.

## <a name="high-availability-deployment"></a>Implantação de alta disponibilidade

O diagrama seguinte mostra uma implementação completa de HA equilibrada em carga do Azure MFA e seus componentes, juntamente com a ADFS para referência.

 ![Implementação do Servidor Azure MFA HA](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Tenha em anotados os seguintes itens para a área numerada correspondentedo do diagrama anterior.

1. Os dois Servidores Azure MFA (MFA1 e MFA2) são equilibrados em carga (mfaapp.contoso.com) e estão configurados para utilizar uma porta estática (4443) para replicar a base de dados PhoneFactor.pfdata. O Serviço Web SDK está instalado em cada um dos Servidores MFA para permitir a comunicação sobre a porta TCP 443 com os servidores ADFS. Os servidores MFA são implantados numa configuração apátrida equilibrada em carga. No entanto, se quiser usar OTP em vez de SMS, deve utilizar um equilíbrio de carga audato.
   ![Servidor Azure MFA - Servidor de aplicações HA](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Uma vez que o RPC utiliza portas dinâmicas, não é aconselhável abrir firewalls até à gama de portas dinâmicas que o RPC pode potencialmente utilizar. Se tiver uma firewall **entre os** seus servidores de aplicações MFA, deve configurar o Servidor MFA para comunicar numa porta estática para o tráfego de replicação entre servidores subordinados e mestres e abrir essa porta na sua firewall. Pode forçar a porta estática criando um ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` valor ```Pfsvc_ncan_ip_tcp_port``` de registo DWORD a pedido e definindo o valor para uma porta estática disponível. As ligações são sempre iniciadas pelos Servidores MFA subordinados ao mestre, a porta estática só é necessária no mestre, mas como você pode promover um subordinado para ser o mestre a qualquer momento, você deve definir a porta estática em todos os Servidores MFA.

2. Os dois servidores de aplicações móveis Do Portal do Utilizador/MFA (MFA-UP-MAS1 e MFA-UP-MAS2) são de carga equilibrados numa configuração **imponente** (mfa.contoso.com). Lembre-se que as sessões pegajosas são um requisito para o equilíbrio de carga do Portal de Utilizador MFA e do Serviço de Aplicações Móveis.
   ![Servidor Azure MFA - Portal do Utilizador e Serviço de Aplicações Móveis HA](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. A quinta ADFS Server é equilibrada e publicada na Internet através de proxies ADFS equilibrados em carga na rede de perímetro. Cada Servidor ADFS utiliza o agente ADFS para comunicar com os Servidores Azure MFA utilizando um único URL (mfaapp.contoso.com) equilibrado em carga sobre a porta TCP 443.

## <a name="next-steps"></a>Passos seguintes

* [Instalar e configurar o Servidor Azure MFA](howto-mfaserver-deploy.md)
