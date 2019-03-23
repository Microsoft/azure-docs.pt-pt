---
title: Autenticação do IIS e servidor do Azure MFA - o Azure Active Directory
description: A implementar a autenticação IIS e Servidor Multi-Factor Authentication do Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6404356edca606d78656011b9dec654e9f29edd3
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368582"
---
# <a name="configure-azure-multi-factor-authentication-server-for-iis-web-apps"></a>Configurar o Servidor Multi-Factor Authentication do Azure para aplicações Web IIS

Utilize a secção Autenticação do IIS do Servidor do Multi-Factor Authentication (MFA) do Azure que permite ativar e configurar a autenticação do IIS para integração com aplicações Web do Microsoft IIS. O Servidor do MFA do Azure instala um plug-in que pode filtrar os pedidos efetuados para o servidor Web do IIS para adicionar o Multi-Factor Authentication do Azure. O plug-in do IIS fornece suporte para Autenticação Baseada em Formulários e Autenticação HTTP Integrada do Windows. Também podem ser configurados IPs Fidedignos para excluir os endereços IP internos da autenticação de dois fatores.

![Autenticação do IIS no servidor MFA](./media/howto-mfaserver-iis/iis.png)

## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Utilizar a Autenticação do IIS Baseada em Formulários com o Servidor Multi-Factor Authentication do Azure

Para proteger uma aplicação Web IIS que utilize autenticação baseada em formulários, instale o Servidor Multi-Factor Authentication do Azure no servidor Web do IIS e configure o Servidor consoante o procedimento seguinte:

1. No Servidor Multi-Factor Authentication do Azure, clique no ícone Autenticação do IIS no menu da esquerda.
2. Clique no separador **Baseado em Formulários**.
3. Clique em **Adicionar**.
4. Para detetar automaticamente as variáveis nome de utilizador, palavra-passe e domínio, introduza o URL de início de sessão (como `https://localhost/contoso/auth/login.aspx`) dentro da caixa de diálogo autoconfigurar Web site e clique em **OK**.
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
4. Na caixa de diálogo Adicionar URL Base, introduza o URL para o Web site onde é feita a autenticação HTTP (como <http://localhost/owa>) e forneça um nome de aplicação (opcional). O nome da Aplicação aparece nos relatórios do Multi-Factor Authentication do Azure e poderá ser apresentado nas mensagens de autenticação SMS ou da Aplicação Móvel.
5. Ajuste o tempo limite de inatividade e os tempos máximos de sessão, se a predefinição não for suficiente.
6. Marque a caixa **Exigir correspondência de utilizador Multi-Factor Authentication** se todos os utilizadores tiverem sido ou forem importados para o Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de utilizadores ainda não tiverem sido importados para o Servidor de e/ou estiverem excluídos da autenticação multifator, deixe a caixa desmarcada.
7. Selecione a caixa da **Cache de cookies**, se pretender.
8. Clique em **OK**.

## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Ativar Plug-ins do IIS para o Servidor Multi-Factor Authentication do Azure

Depois de configurar os URLs e as definições de autenticação HTTP ou Baseada em Formulários, selecione as localizações onde os plug-ins do IIS do Multi-Factor Authentication do Azure devem ser carregados e ativados no IIS. Utilize o seguinte procedimento:

1. Se executar no IIS 6, clique no separador **ISAPI**. Selecione o site no qual a aplicação Web está a ser executada (por exemplo, Site Predefinido) para ativar o plug-in do filtro ISAPI do Multi-Factor Authentication do Azure para esse site.
2. Se executar no IIS 7 ou superior, clique no separador **Módulo Nativo**. Selecione o servidor, sites ou aplicações para ativar o plug-in do IIS nos níveis pretendidos.
3. Clique na caixa **Ativar autenticação do IIS** na parte superior do ecrã. O Multi-Factor Authentication do Azure está agora a proteger a aplicação IIS selecionada. Certifique-se de que os utilizadores foram importados para o Servidor.

## <a name="trusted-ips"></a>IPs Fidedignos

Os IPs Fidedignos permitem aos utilizadores ignorar o Multi-Factor Authentication do Azure para pedidos de sites com origem em sub-redes ou endereços IP específicos. Por exemplo, pode pretender excluir utilizadores do Multi-Factor Authentication do Azure ao iniciar sessão a partir do escritório. Para tal, especifique a sub-rede do escritório como uma entrada de IPs Fidedignos. Para configurar IPs Fidedignos, utilize o seguinte procedimento:

1. Na secção Autenticação do IIS, clique no separador **IPs Fidedignos**.
2. Clique em **Adicionar**.
3. Quando for apresentada a caixa de diálogo Adicionar IPs Fidedignos, selecione o botão de opção **IP único**, **Intervalo de IPs** ou **Sub-rede**.
4. Introduza o endereço IP, o intervalo de endereços IP ou a sub-rede a colocar na lista de permissões. Se introduzir uma sub-rede, selecione a Máscara de rede adequada e clique em **OK**. A lista de permissões foi agora adicionada.
