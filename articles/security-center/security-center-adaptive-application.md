---
title: Controlos de aplicações adaptativos no Centro de Segurança do Azure | Microsoft Docs
description: Este documento ajuda-o a utilizar os controlos de aplicações adaptativos no Centro de Segurança do Azure para adicionar as aplicações em execução em VMs do Azure à lista de permissões.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2019
ms.author: monhaber
ms.openlocfilehash: cbda94b8ceeaf7a225117e1ca73445135a32a243
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089000"
---
# <a name="adaptive-application-controls-in-azure-security-center"></a>Controlos de aplicações adaptáveis no Centro de Segurança do Azure
Saiba como configurar o controlo de aplicações no Centro de Segurança do Azure com estas instruções.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>O que são os controlos de aplicações adaptativos do Centro de Segurança?
Controlo de aplicação adaptável é uma solução de listas de permissões de aplicações de ponto-a-ponto de inteligente e automatizadas do Centro de segurança do Azure. Ele ajuda a controlar que aplicações podem ser executadas nas suas VMs localizadas no Azure, que, entre outros benefícios, ajuda a proteger as VMs contra software maligno. Centro de segurança utiliza machine learning para analisar as aplicações em execução nas suas VMs e ajuda-o a aplicar regras de inclusão específico usando nessas informações. Esta capacidade simplifica grandemente o processo de configuração e manutenção de políticas de listas de permissões de aplicações, permitindo-lhe:

- Bloquear ou alertar relativamente a tentativas de executar aplicativos mal-intencionados, incluindo aqueles que possam não ser detetadas pelas soluções de antimalware.
- Estar em conformidade com a política de segurança da sua organização que estabelece que só pode ser utilizado software licenciado.
- Evitar a utilização de software não desejado no seu ambiente.
- Evitar a execução de aplicações antigas e não suportadas.
- Impedir ferramentas de software específicas que não são permitidas na sua organização.
- Permitir que a equipa de TI controle o acesso a dados confidenciais através da utilização de aplicações.

## <a name="how-to-enable-adaptive-application-controls"></a>Como ativar os controlos de aplicações adaptáveis?
Controlos de aplicações adaptáveis ajudam a definir um conjunto de aplicações que têm permissão para executar em grupos configurados de VMs. Esta funcionalidade só está disponível para máquinas Windows (todas as versões, clássica ou Azure Resource Manager). Os passos seguintes podem ser utilizados para configurar a inclusão de aplicações nas listas de permissões do Centro de Segurança:

1. Abra o dashboard **Centro de Segurança**.
2. No painel esquerdo, selecione **Controlos de aplicação adaptáveis** localizados em **Defesa da cloud avançada**.

    ![Defesa](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)

É apresentada a página **Controlos de aplicação adaptável**.

![controlos](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

A secção **Grupos de VMs** contém três separadores:

* **Configurados**: lista de grupos que contêm as VMs que foram configuradas com o controlo de aplicações.
* **Recomendados**: lista de grupos para os quais o controlo de aplicações é recomendado. O Centro de Segurança utiliza machine learning para identificar as VMs que são boas candidatas para o controlo de aplicações com base no facto de executarem consistentemente as mesmas aplicações.
* **Nenhuma recomendação**: lista de grupos que contêm as VMs para as quais não existem recomendações de controlo de aplicações. Por exemplo, VMs em que as aplicações estão sempre a ser alteradas e que não atingiram um estado estável.

> [!NOTE]
> O Centro de Segurança utiliza um algoritmo proprietário de clustering para criar grupos de VMs, garantindo que as VMs semelhantes têm uma política de controlo de aplicações recomendada ideal.
>
>

### <a name="configure-a-new-application-control-policy"></a>Configurar uma política de controlo de aplicações nova
1. Clique no separador **Recomendados** para ver uma lista dos grupos com recomendações de controlo de aplicações:

   ![Recomendado](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   A lista inclui:

   - **NOME**: o nome do grupo e da subscrição
   - **VMs**: o número de máquinas virtuais no grupo
   - **ESTADO**: o estado das recomendações
   - **GRAVIDADE**: o nível de gravidade das recomendações

2. Clique num grupo para abrir o **criar regras de controlo de aplicação** opção.

   ![Regras de controlo de aplicações](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. Na **selecionar VMs**, reveja a lista de VMs recomendadas e desmarque aquelas às não pretende aplicar uma política de lista de permissões de aplicações para. Em seguida, verá duas listas:

   - **Aplicações recomendadas**: uma lista de aplicações que são frequentes nas VMs neste grupo e são recomendados para permissão para ser executada.
   - **Mais aplicações**: uma lista de aplicativos que são menos frequentes nas VMs neste grupo ou que são conhecidas como exploráveis (ver mais abaixo) e recomendadas para revisão.

4. Reveja as aplicações em cada lista e desmarque as que não pretende aplicar. Cada lista inclui:

   - **NOME**: as informações do certificado ou o caminho completo de uma aplicação
   - **TIPOS DE FICHEIRO**: o tipo de ficheiro da aplicação. Isso pode ser EXE, Script, MSI ou qualquer permuta desses tipos.
   - **EXPLORÁVEIS**: um ícone de aviso indica se uma aplicação específica poderia ser usada por um atacante para ignorar uma solução de listas de permissões de aplicações. Recomenda-se que reveja estas aplicações antes da respetiva aprovação.
   - **UTILIZADORES**: os utilizadores que são recomendados para permissão de execução de uma aplicação

5. Depois de concluir as suas seleções, selecione **Criar**. <br>
   Depois de selecionar criar, o Centro de segurança do Azure cria automaticamente as regras adequadas sobre a solução de listas de permissões de aplicações incorporadas disponível nos servidores do Windows (AppLocker).

> [!NOTE]
> - O Centro de Segurança precisa de um mínimo de duas semanas de dados para criar uma linha de base e preencher as recomendações exclusivas por grupo de VMs. Os novos clientes do escalão standard do Centro de Segurança devem esperar um comportamento em que, primeiro, os grupos de VMs são apresentados no separador *nenhuma recomendação*.
> - Os Controlos de Aplicações Adaptáveis do Centro de Segurança não suportam VMs para as quais já esteja ativada uma política do AppLocker por um GPO ou uma política de segurança local.
> -  Como uma melhor prática, o Centro de segurança tentará sempre de segurança para criar uma regra de publicador para aplicações que estão selecionados para ser permitido e apenas se um aplicativo não tem informações do publicador (também conhecido como não assinadas), será criada uma regra de caminho para o caminho completo dos aplicação específica.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Editar e monitorizar grupos configurados com o controlo de aplicações

1. Para editar e monitorizar um grupo configurado com uma política de lista de permissões de aplicação, volte para o **controlos de aplicação adaptável** página e selecione **configurado** sob **gruposdeVMs**:

   ![Grupos](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   A lista inclui:

   - **Nome**: o nome da subscrição e grupo
   - **VMs**: o número de máquinas virtuais no grupo
   - **Modo**: Modo de auditoria vai registar as tentativas de executar aplicações de não-lista de permissões; Impor irá não permitir a execução de aplicativos não-lista de permissões
   - **Alertas**: todas as violações atuais

2. Clique num grupo para fazer alterações na **Editar política de controlo de aplicação** página.

   ![Proteção](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. No **Modo de proteção**, tem a possibilidade de selecionar entre o seguinte:

   - **Auditoria**: neste modo, a solução de controlo de aplicações não impõe as regras e realiza apenas a auditoria à atividade nas VMs protegidas. Esta opção é recomendada para cenários nos quais pretende primeiro observar o comportamento geral antes de bloquear uma aplicação para ser executada na VM de destino.
   - **Impor**: neste modo, a solução de controlo de aplicações impõe as regras e certifica-se de que as aplicações que não têm autorização para ser executadas são bloqueadas.

   > [!NOTE]
   > -  **Impor** modo de proteção está desativado até indicações em contrário.
   > - Conforme mencionado anteriormente, por predefinição, é sempre configurada uma política de controlo de aplicações nova no modo *Auditoria*. 
   >

4. Sob **extensão de política**, adicionar qualquer caminho de aplicação que pretende permitir. Depois de adicionar estes caminhos, o Centro de segurança atualiza a política de lista de permissões de aplicações nas VMs dentro do grupo selecionado de VMS e cria as regras adequadas para estas aplicações, além das regras que já estão em vigor.

5. Rever as violações atuais listadas na **alertas recentes** secção. Clique em cada linha ser redirecionado para o **alertas** página dentro do Centro de segurança do Azure e ver todos os alertas que foram detetados pelo centro de segurança do Azure nas VMs associadas.
   - **Alertas**: todas as violações que foram registadas.
   - **Não. de VMs**: o número de máquinas virtuais com este tipo de alerta.

6. Sob **regras de inclusão do publicador**, **regras de inclusão do caminho**, e **regras de inclusão de Hash** pode ver quais permissões de aplicação regras estão atualmente configurado nas VMs dentro de um grupo, de acordo com o tipo de coleção de regra. Para cada regra, pode ver:

   - **Regra**: Os parâmetros específicos de acordo com que um aplicativo é examinado pelo AppLocker para determinar se um aplicativo pode ser executada.
   - **Tipo de ficheiro**: Os tipos de ficheiros que são abrangidos por uma regra específica. Isto pode ser qualquer um dos seguintes: EXE, Script, MSI ou qualquer permuta desses tipos de ficheiro.
   - **Os utilizadores**: Nome ou número de utilizadores que têm permissão para executar um aplicativo que é abrangido por uma regra de lista de permissões de aplicação.

   ![Regras de inclusão em listas de permissões](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Clique nos três pontos no final de cada linha, se pretender eliminar a regra específica ou editar os utilizadores permitidos.

8. Depois de efetuar alterações para um **controlos de aplicação adaptável** política, clique em **guardar**.

### <a name="not-recommended-list"></a>Lista de não recomendadas

Centro de segurança só recomenda a políticas de listas de permissões de aplicações para máquinas virtuais que executem um conjunto de aplicações estável. Não são criadas recomendações se as aplicações nas VMs associadas forem constantemente alteradas.

![Recomendação](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

A lista contém:
- **NOME**: o nome do grupo e da subscrição
- **VMs**: o número de máquinas virtuais no grupo

Centro de segurança do Azure permite-lhe definir uma política de lista de permissões de aplicações em não recomendado grupos de VMs também. Siga os mesmos princípios, tal como foi descrito anteriormente, para configurar uma política de lista de permissões de aplicações nesses grupos também.


## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a utilizar os controlos de aplicações adaptativos no Centro de Segurança do Azure para adicionar as aplicações em execução em VMs do Azure à lista de permissões. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerir alertas e responder a incidentes de segurança no Centro de Segurança.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Compreender os alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de Resolução de Problemas do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como resolver problemas comuns no Centro de Segurança.
* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
