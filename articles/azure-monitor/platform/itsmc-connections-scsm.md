---
title: Ligue o SCSM ao Conector de Gestão de Serviços de TI
description: Este artigo fornece informações sobre como scsm com o It Service Management Connector (ITSMC) em Azure Monitor para monitorizar e gerir centralmente os itens de trabalho ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 79706b66dba46253843b1f53a26481170d6ff723
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729729"
---
# <a name="connect-system-center-service-manager-with-it-service-management-connector"></a>Connect System Center Service Manager com it service management connector

Este artigo fornece informações sobre como configurar a ligação entre a instância do seu Gestor de Serviços do System Center e o Conector de Gestão de Serviços de TI (ITSMC) no Log Analytics para gerir centralmente os seus itens de trabalho.

As secções seguintes fornecem detalhes sobre como ligar o seu produto System Center Service Manager ao ITSMC em Azure.

## <a name="prerequisites"></a>Pré-requisitos

Assegurar que os seguintes pré-requisitos sejam cumpridos:

- ITSMC instalado. Mais informações: [Adicionar a Solução de Conector de Gestão de Serviços de TI](./itsmc-definition.md).
- A aplicação Web do Gestor de Serviços (web app) está implantada e configurada. A informação sobre a aplicação Web está [aqui.](#create-and-deploy-service-manager-web-app-service)
- Ligação híbrida criada e configurada. Mais informações: [Configurar a Ligação Híbrida](#configure-the-hybrid-connection).
- Versões suportadas do Service Manager: 2012 R2 ou 2016.
- Função de utilizador:  [Operador avançado](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10)).
- Hoje os alertas enviados pelo Azure Monitor podem criar em Incidentes do Gestor de Serviços do System Center.

> [!NOTE]
> - O Conector ITSM só pode ligar-se a instâncias de ServiceNow baseadas na nuvem. As instâncias do ServiceNow no local não são suportadas atualmente.
> - Para utilizar [modelos personalizados](./itsmc-definition.md#template-definitions) como parte das ações, o parâmetro "ProjectionType" no modelo SCSM deve ser mapeado para "IncidentManagement! System.WorkItem.Incident.ProjectionType"

## <a name="connection-procedure"></a>Procedimento de ligação

Utilize o seguinte procedimento para ligar a sua instância do Gestor de Serviços do System Center ao ITSMC:

1. No portal Azure, vá a **Todos os Recursos** e procure o **ServiceDesk (Seu Nome De Espaço-Trabalho)**

2. No **âmbito do WORKSPACE DATA SOURCES** clique em **ItsM Connections**.

    ![Nova ligação](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações como descrito na tabela seguinte e clique **em OK** para criar a ligação.

> [!NOTE]
> Todos estes parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome de conexão**   | Digite um nome para a instância do Gestor de Serviço do Centro de Sistema que pretende ligar com o ITSMC.  Utilize este nome mais tarde quando configurar itens de trabalho neste caso/ ver análises detalhadas de registos. |
| **Tipo de parceiro**   | Selecione **System Center Service Manager**. |
| **URL do servidor**   | Digite o URL da aplicação Web do Gestor de Serviço. Mais informações sobre a aplicação Web do Service Manager [estão aqui.](#create-and-deploy-service-manager-web-app-service)
| **ID de Cliente**   | Digite o ID do cliente que gerou (utilizando o script automático) para autenticar a aplicação Web. Mais informações sobre o script automatizado [estão aqui.](./itsmc-service-manager-script.md)|
| **Segredo do Cliente**   | Digite o segredo do cliente, gerado para esta identificação.   |
| **Dados de Sincronização**   | Selecione os itens de trabalho do Gestor de Serviço que pretende sincronizar através do ITSMC.  Estes artigos de trabalho são importados para o Log Analytics. **Opções:**  Incidentes, Pedidos de Alteração.|
| **Âmbito de sincronização de dados** | Digite o número de dias passados de onde deseja os dados. **Limite máximo:** 120 dias. |
| **Criar novo item de configuração na solução ITSM** | Selecione esta opção se pretender criar os itens de configuração no produto ITSM. Quando selecionado, o Log Analytics cria os CIs afetados como itens de configuração (em caso de TEC não existentes) no sistema ITSM suportado. **Predefinição:** desativado. |

![Conexão de gestor de serviço](media/itsmc-connections/service-manager-connection.png)

**Quando conectado com sucesso, e sincronizado:**

- Os artigos de trabalho selecionados do Service Manager são importados para a Azure **Log Analytics.** Pode ver o resumo destes artigos de trabalho no azulejo do conector de gestão de serviços de TI.

- Pode criar incidentes a partir de alertas de Log Analytics ou de registos de registos, ou a partir de alertas Azure nesta instância do Service Manager.

Saiba mais: [Crie itens de trabalho ITSM a partir de alertas Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="create-and-deploy-service-manager-web-app-service"></a>Criar e implementar serviço de aplicações web do Service Manager

Para ligar o Gestor de Serviços no local com o ITSMC em Azure, a Microsoft criou uma aplicação Web do Service Manager no GitHub.

Para configurar a aplicação WEB ITSM para o seu Gestor de Serviços, faça o seguinte:

- **Implementar a aplicação Web** – Implementar a aplicação Web, definir as propriedades e autenticar com Azure AD. Pode implementar a aplicação web utilizando o [script automatizado](./itsmc-service-manager-script.md) que a Microsoft lhe forneceu.
- **Configure a ligação**  -  híbrida [Configure esta ligação,](#configure-the-hybrid-connection)manualmente.

### <a name="deploy-the-web-app"></a>Implementar a aplicação web
Utilize o [script](./itsmc-service-manager-script.md) automatizado para implementar a aplicação Web, definir as propriedades e autenticar com Azure AD.

Execute o script fornecendo os seguintes detalhes necessários:

- Detalhes da subscrição do Azure
- Nome do grupo de recursos
- A localização
- Detalhes do servidor do Gestor de Serviço (nome do servidor, domínio, nome de utilizador e palavra-passe)
- Prefixo de nome do site para a sua aplicação Web
- Espaço de nomes ServiceBus.

O script cria a aplicação Web usando o nome especificado (juntamente com algumas cordas adicionais para torná-la única). Gera o URL da **aplicação Web,** **iD do cliente** e **segredo do cliente.**

Guarde os valores, utilize-os quando criar uma ligação com o ITSMC.

**Verifique a instalação da aplicação Web**

1. Ir para o **portal Azure**  >  **Recursos**.
2. Selecione a aplicação Web, clique em  >  **Definições de Aplicação** de Definições .
3. Confirme as informações sobre a instância do Service Manager que forneceu no momento da implementação da aplicação através do script.

## <a name="configure-the-hybrid-connection"></a>Configure a ligação híbrida

Utilize o seguinte procedimento para configurar a ligação híbrida que liga a instância do Gestor de Serviço com o ITSMC em Azure.

1. Encontre a aplicação Web do Gestor de Serviços, ao abrigo **da Azure Resources.**
2. Clique em **Redes de**  >  **Definições**.
3. Em **Conexões Híbridas,** clique em **Configurar os seus pontos finais de ligação híbrida**.

    ![Rede de ligação híbrida](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. Na lâmina **De Ligações Híbridas,** clique **em Adicionar ligação híbrida**.

    ![Adicionar ligação híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. Na lâmina **Add Hybrid Connections,** clique em **Criar uma nova ligação híbrida**.

    ![Nova ligação híbrida](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Digite os seguintes valores:

   - **Nome do Ponto final**: Especifique um nome para a nova ligação Híbrida.
   - **EndPoint Host**: FQDN do servidor de gestão do Gestor de Serviços.
   - **Porta EndPoint**: Tipo 5724
   - **Espaço de nome servicebus**: Utilize um espaço de nome de servicebus existente ou crie um novo.
   - **Localização**: selecione a localização.
   - **Nome**: Especifique um nome para obus de serviço se estiver a criá-lo.

     ![Valores de ligação híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Clique **em OK** para fechar a lâmina de **ligação híbrida Create** e comece a criar a ligação híbrida.

    Uma vez criada a ligação Híbrida, é exibida debaixo da lâmina.

7. Depois da ligação híbrida ser criada, selecione a ligação e clique **Em Adicionar a ligação híbrida selecionada**.

    ![Nova ligação híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

### <a name="configure-the-listener-setup"></a>Configure a configuração do ouvinte

Utilize o seguinte procedimento para configurar a configuração do ouvinte para a ligação híbrida.

1. Na lâmina **De Ligações Híbridas,** clique em **Baixar o Gestor de Ligação** e instale-o na máquina onde está a decorrer a instância do Gestor de Serviços do Centro de Sistema.

    Uma vez concluída a instalação, a opção **UI do Gestor de Ligação Híbrida** está disponível no menu **Iniciar.**

2. Clique **em Hybrid Connection Manager UI,** será solicitado para as suas credenciais Azure.

3. Faça login com as suas credenciais Azure e selecione a sua subscrição onde foi criada a ligação Híbrida.

4. Clique em **Guardar**.

A sua ligação híbrida está ligada com sucesso.

![conexão híbrida de sucesso](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> Após a criação da ligação híbrida, verifique e teste a ligação visitando a aplicação Web do Service Manager implementada. Certifique-se de que a ligação é bem sucedida antes de tentar ligar-se ao ITSMC em Azure.

A imagem da amostra que se segue mostra os detalhes de uma ligação bem sucedida:

![Teste de conexão híbrido](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do conector ITSM](itsmc-overview.md)
* [Crie itens de trabalho ITSM a partir de alertas Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Resolver problemas do Conector do ITSM](./itsmc-resync-servicenow.md)