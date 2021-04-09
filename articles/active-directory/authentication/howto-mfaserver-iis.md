---
title: Autenticação IIS e Servidor Azure MFA - Diretório Ativo Azure
description: A implementar a autenticação IIS e Servidor Multi-Factor Authentication do Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c522fb950e2489afce44553d233ac3eddf138730
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742175"
---
# <a name="configure-azure-multi-factor-authentication-server-for-iis-web-apps"></a>Configurar o Servidor Multi-Factor Authentication do Azure para aplicações Web IIS

Utilize a secção Autenticação do IIS do Servidor do Multi-Factor Authentication (MFA) do Azure que permite ativar e configurar a autenticação do IIS para integração com aplicações Web do Microsoft IIS. O Servidor do MFA do Azure instala um plug-in que pode filtrar os pedidos efetuados para o servidor Web do IIS para adicionar o Multi-Factor Authentication do Azure. O plug-in do IIS fornece suporte para Autenticação Baseada em Formulários e Autenticação HTTP Integrada do Windows. Também podem ser configurados IPs Fidedignos para excluir os endereços IP internos da autenticação de dois fatores.

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft já não oferece O MFA Server para novas implementações. Os novos clientes que pretendam exigir a autenticação de vários fatores (MFA) durante os eventos de entrada devem utilizar a autenticação multi-factor Azure AD baseada na nuvem.
>
> Para começar com mFA baseado na nuvem, consulte [Tutorial: Secure user in events with Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Os clientes existentes que ativaram o MFA Server antes de 1 de julho de 2019 podem descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.
>
> Quando utiliza a autenticação multi-factor Azure baseada na nuvem, não existe alternativa ao plugin IIS fornecido pelo Azure Multi-Factor Authentication (MFA) Server. Em vez disso, utilize o Proxy de Aplicação Web (WAP) com serviços da Federação de Diretórios Ativos (AD FS) ou o Proxy de Aplicação do Diretório Ativo da Azure.

![Autenticação do IIS no Servidor MFA](./media/howto-mfaserver-iis/iis.png)

## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Utilizar a Autenticação do IIS Baseada em Formulários com o Servidor Multi-Factor Authentication do Azure

Para proteger uma aplicação Web IIS que utilize autenticação baseada em formulários, instale o Servidor Multi-Factor Authentication do Azure no servidor Web do IIS e configure o Servidor consoante o procedimento seguinte:

1. No Servidor Multi-Factor Authentication do Azure, clique no ícone Autenticação do IIS no menu da esquerda.
2. Clique no separador **Baseado em Formulários**.
3. Clique em **Adicionar**.
4. Para detetar automaticamente o nome de utilizador, palavra-passe e variáveis de domínio, introduza o URL de login `https://localhost/contoso/auth/login.aspx` (como) dentro da caixa de diálogo do site Form-Based Configurar automaticamente e clique **em OK**.
5. Marque a caixa **Exigir correspondência de utilizador Multi-Factor Authentication** se todos os utilizadores tiverem sido ou forem importados para o Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de utilizadores ainda não tiverem sido importados para o Servidor de e/ou estiverem excluídos da autenticação multifator, deixe a caixa desmarcada.
6. Se as variáveis de página não puderem ser detetadas automaticamente, clique em **Especificar Manualmente** na caixa de diálogo Configurar Automaticamente Site Baseado em Formulário.
7. Na caixa de diálogo Adicionar Web Site Baseado em Formulários, introduza o URL para a página de início de sessão no campo URL de Submissão e introduza um nome de Aplicação (opcional). O nome da Aplicação aparece nos relatórios do Multi-Factor Authentication do Azure e poderá ser apresentado nas mensagens de autenticação SMS ou da Aplicação Móvel.
8. Selecione o formato correto do Pedido. Está definido como **POST ou GET** para a maioria das aplicações Web.
9. Introduza a variável de Nome de Utilizador, Palavra-passe e Domínio (se aparecer na página de início de sessão). para localizar os nomes das caixas de entrada, navegue num browser, clique com o botão direito do rato na página e selecione **Ver Origem** .
10. Selecione a caixa de **correspondência de utilizador Exigir autenticação Azure Multi-Factor Authentication** se todos os utilizadores tiverem sido ou forem importados para o Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de utilizadores ainda não tiverem sido importados para o Servidor de e/ou estiverem excluídos da autenticação multifator, deixe a caixa desmarcada.
11. Clique em **Avançadas**, para rever as definições avançadas, incluindo:

    - Selecione um ficheiro de página de rejeição personalizado
    - Coloque em cache as autenticações bem-sucedidas para o Site durante um período de tempo através de cookies
    - Selecione se pretende autenticar as credenciais primárias em relação a um Domínio do Windows, ao diretório LDAP ou ao servidor RADIUS.

12. Clique em **OK** para regressar à caixa de diálogo Adicionar Web site Baseado em Formulários.
13. Clique em **OK**.
14. Assim que as variáveis de URL e página forem detetadas ou introduzidas, os dados do site são apresentados no painel Baseado em Formulários.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>Utilizar a Autenticação Integrada do Windows com o Servidor Multi-Factor Authentication do Azure

Para proteger uma aplicação Web IIS que utilize autenticação HTTP integrada do Windows, instale o Servidor MFA do Azure no servidor Web do IIS e, em seguida, configure o Servidor com os passos seguintes:

1. No Servidor Multi-Factor Authentication do Azure, clique no ícone Autenticação do IIS no menu da esquerda.
2. Clique no separador **HTTP**.
3. Clique em **Adicionar**.
4. Na caixa de diálogo DE URL De Base Adicionar, insira o URL para o website onde a autenticação HTTP é realizada `http://localhost/owa` (como) e forneça um nome de Aplicação (opcional). O nome da Aplicação aparece nos relatórios do Multi-Factor Authentication do Azure e poderá ser apresentado nas mensagens de autenticação SMS ou da Aplicação Móvel.
5. Ajuste o tempo limite de inatividade e os tempos máximos de sessão, se a predefinição não for suficiente.
6. Marque a caixa **Exigir correspondência de utilizador Multi-Factor Authentication** se todos os utilizadores tiverem sido ou forem importados para o Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de utilizadores ainda não tiverem sido importados para o Servidor de e/ou estiverem excluídos da autenticação multifator, deixe a caixa desmarcada.
7. Selecione a caixa da **Cache de cookies**, se pretender.
8. Clique em **OK**.

## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Ativar Plug-ins do IIS para o Servidor Multi-Factor Authentication do Azure

Depois de configurar os URLs e as definições de autenticação HTTP ou Baseada em Formulários, selecione as localizações onde os plug-ins do IIS do Multi-Factor Authentication do Azure devem ser carregados e ativados no IIS. Utilize o seguinte procedimento:

1. Se estiver a funcionar no IIS 6, clique no separador **ISAPI.** Selecione o website que a aplicação web está a ser executada (por exemplo, Web Site padrão) para ativar o plug-in do filtro isapi de autenticação multi-factor Azure para esse site.
2. Se estiver a funcionar no IIS 7 ou superior, clique no **separador Módulo Nativo.** Selecione o servidor, websites ou aplicações para ativar o plug-in IIS nos níveis pretendidos.
3. Clique na caixa **Ativar autenticação do IIS** na parte superior do ecrã. O Multi-Factor Authentication do Azure está agora a proteger a aplicação IIS selecionada. Certifique-se de que os utilizadores foram importados para o Servidor.

## <a name="trusted-ips"></a>IPs Fidedignos

Os IPs Fidedignos permitem aos utilizadores ignorar o Multi-Factor Authentication do Azure para pedidos de sites com origem em sub-redes ou endereços IP específicos. Por exemplo, pode pretender excluir utilizadores do Multi-Factor Authentication do Azure ao iniciar sessão a partir do escritório. Para tal, especifique a sub-rede do escritório como uma entrada de IPs Fidedignos. Para configurar IPs Fidedignos, utilize o seguinte procedimento:

1. Na secção Autenticação do IIS, clique no separador **IPs Fidedignos**.
2. Clique em **Adicionar**.
3. Quando for apresentada a caixa de diálogo Adicionar IPs Fidedignos, selecione o botão de opção **IP único**, **Intervalo de IPs** ou **Sub-rede**.
4. Introduza o endereço IP, intervalo de endereços IP ou sub-redes que devem ser permitidos. Se introduzir uma sub-rede, selecione a Máscara de rede adequada e clique em **OK**.
