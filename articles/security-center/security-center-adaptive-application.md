---
title: Controlos de aplicações adaptáveis no Centro de Segurança do Azure
description: Este documento ajuda você a usar o controle de aplicativo adaptável na central de segurança do Azure para aplicativos de lista de permissões em execução em computadores do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/23/2019
ms.author: memildin
ms.openlocfilehash: 862fb4f8a9dcd357148f73a729ffc7e92ba0083a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75353430"
---
# <a name="adaptive-application-controls"></a>Controlos de aplicação adaptativos
Saiba como configurar o controlo de aplicações no Centro de Segurança do Azure com estas instruções.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>O que são os controlos de aplicações adaptativos do Centro de Segurança?
O controle de aplicativo adaptável é uma solução inteligente, automatizada e de ponta a ponta da central de segurança do Azure, que ajuda você a controlar quais aplicativos podem ser executados em suas máquinas do Azure e não Azure (Windows e Linux). Entre outros benefícios, isso ajuda a proteger seus computadores contra malware. A central de segurança usa o aprendizado de máquina para analisar os aplicativos em execução em seus computadores e cria uma lista de permissões dessa inteligência. Esse recurso simplifica muito o processo de configuração e manutenção de políticas de lista de permissões de aplicativo, permitindo que você:

- Bloquear ou alertar tentativas de execução de aplicativos mal-intencionados, incluindo aqueles que poderiam ser perdidos por soluções antimalware.
- Estar em conformidade com a política de segurança da sua organização que estabelece que só pode ser utilizado software licenciado.
- Evitar a utilização de software não desejado no seu ambiente.
- Evitar a execução de aplicações antigas e não suportadas.
- Impedir ferramentas de software específicas que não são permitidas na sua organização.
- Permitir que a equipa de TI controle o acesso a dados confidenciais através da utilização de aplicações.

> [!NOTE]
> Para computadores não Azure e Linux, os controles de aplicativo adaptáveis têm suporte somente no modo de auditoria.

## <a name="how-to-enable-adaptive-application-controls"></a>Como ativar os controlos de aplicações adaptáveis?

Controles de aplicativo adaptáveis ajudam a definir um conjunto de aplicativos que têm permissão para serem executados em grupos de computadores configurados. Esse recurso está disponível para Windows Azure e não Azure (todas as versões, clássicas ou Azure Resource Manager) e computadores Linux. Use as etapas a seguir para configurar suas listas de permissões de aplicativo:

1. Abra o dashboard **Centro de Segurança**.

1. No painel esquerdo, selecione **Controlos de aplicação adaptáveis** localizados em **Defesa da cloud avançada**.

    [![defesa](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png#lightbox)

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

1. Selecione a guia **recomendado** para obter uma lista de grupos com recomendações de controle de aplicativo:

   ![Recomendado](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   A lista inclui:

   - **Nome do grupo**: o nome da assinatura e do grupo
   - **VMs e computadores**: o número de máquinas virtuais no grupo
   - **Estado**: o estado das recomendações
   - **Severidade**: o nível de severidade das recomendações

2. Clique em um grupo para abrir a opção **criar regras de controle de aplicativo** .

   [regras de controle de aplicativo ![](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png#lightbox)

3. Em **selecionar VMs**, examine a lista de VMs recomendadas e desmarque a que você não deseja aplicar uma política de lista de permissões de aplicativo. Em seguida, verá duas listas:

   - **Aplicativos recomendados**: uma lista de aplicativos que são frequentes nas VMs dentro deste grupo e que têm a permissão de serem executadas.
   - **Mais aplicativos**: uma lista de aplicativos que são menos frequentes nas VMs dentro deste grupo ou que são conhecidas como Exploitables (veja mais abaixo) e recomendadas para revisão.

4. Reveja as aplicações em cada lista e desmarque as que não pretende aplicar. Cada lista inclui:

   - **Nome**: as informações do certificado ou o caminho completo de um aplicativo
   - **TIPOS DE FICHEIRO**: o tipo de ficheiro da aplicação. Isso pode ser EXE, script, MSI ou qualquer permutação desses tipos.
   - **Explorável**: um ícone de aviso indica se um aplicativo específico pode ser usado por um invasor para ignorar uma lista de permissões de aplicativo. Recomenda-se que reveja estas aplicações antes da respetiva aprovação.
   - **UTILIZADORES**: os utilizadores que são recomendados para permissão de execução de uma aplicação

5. Depois de concluir as suas seleções, selecione **Criar**. <br>
   Depois de selecionar criar, a central de segurança do Azure cria automaticamente as regras apropriadas sobre a solução de lista de permissões de aplicativo interna disponível em Windows Servers (AppLocker).

> [!NOTE]
> - O Centro de Segurança precisa de um mínimo de duas semanas de dados para criar uma linha de base e preencher as recomendações exclusivas por grupo de VMs. Os novos clientes do escalão standard do Centro de Segurança devem esperar um comportamento em que, primeiro, os grupos de VMs são apresentados no separador *nenhuma recomendação*.
> - Os Controlos de Aplicações Adaptáveis do Centro de Segurança não suportam VMs para as quais já esteja ativada uma política do AppLocker por um GPO ou uma política de segurança local.
> -  Como prática recomendada de segurança, a central de segurança sempre tentará criar uma regra de editor para aplicativos selecionados para serem permitidos e somente se um aplicativo não tiver informações do Publicador (também conhecido como não assinado), uma regra de caminho será criada para o caminho completo do aplicativo específico.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Editar e monitorizar grupos configurados com o controlo de aplicações

1. Para editar e monitorar um grupo configurado com uma política de lista de permissões de aplicativo, retorne à página de **controles de aplicativo adaptáveis** e selecione **configurado** em **grupos de VMs**:

   ![Grupos](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   A lista inclui:

   - **Nome do grupo**: o nome da assinatura e do grupo
   - **VMs e computadores**: o número de máquinas virtuais no grupo
   - **Modo**: o modo de auditoria registrará tentativas de execução de aplicativos que não estão na lista de permissões; Impor não permitirá que os aplicativos sejam executados, a menos que estejam na lista de permissões
   - **Alertas**: quaisquer violações atuais

2. Clique em um grupo para fazer alterações na página **Editar política de controle de aplicativo** .

   ![Proteção](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. No **Modo de proteção**, tem a possibilidade de selecionar entre o seguinte:

   - **Auditoria**: neste modo, a solução de controlo de aplicações não impõe as regras e realiza apenas a auditoria à atividade nas VMs protegidas. Esta opção é recomendada para cenários nos quais pretende primeiro observar o comportamento geral antes de bloquear uma aplicação para ser executada na VM de destino.
   - **Impor**: neste modo, a solução de controlo de aplicações impõe as regras e certifica-se de que as aplicações que não têm autorização para ser executadas são bloqueadas.

   > [!NOTE]
   > -  A **aplicação** do modo de proteção está desabilitada até um aviso adicional.
   > - Conforme mencionado anteriormente, por predefinição, é sempre configurada uma política de controlo de aplicações nova no modo *Auditoria*. 
   >

4. Em **extensão de política**, adicione qualquer caminho de aplicativo que você deseja permitir. Depois de adicionar esses caminhos, a central de segurança atualiza a política de lista de permissões de aplicativo nas VMs dentro do grupo selecionado de VMS e cria as regras apropriadas para esses aplicativos, além das regras que já estão em vigor.

5. Examine as violações atuais listadas na seção **alertas recentes** . Clique em cada linha a ser redirecionada para a página **alertas** na central de segurança do Azure e exiba todos os alertas detectados pela central de segurança do Azure nas VMs associadas.
   - **Alertas**: quaisquer violações que foram registradas.
   - **Não. de VMs**: o número de máquinas virtuais com esse tipo de alerta.

6. Em regras de lista de permissões de **Publicador**, **regras de lista**de permissões de caminho e **regras de lista** de permissões de hash, você pode ver quais regras de lista de permissões de aplicativo estão atualmente configuradas nas VMs em um grupo, de acordo com o tipo de coleção de regras Para cada regra, você pode ver:

   - **Regra**: os parâmetros específicos de acordo com o qual um aplicativo é examinado pelo AppLocker para determinar se um aplicativo tem permissão para ser executado.
   - **Tipo de arquivo**: os tipos de arquivo que são cobertos por uma regra específica. Isso pode ser qualquer um dos seguintes: EXE, script, MSI ou qualquer permutação desses tipos de arquivo.
   - **Usuários**: nome ou número de usuários que têm permissão para executar um aplicativo que é coberto por uma regra de lista de permissões de aplicativo.

   ![Regras de inclusão em listas de permissões](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Clique nos três pontos no final de cada linha se você quiser excluir a regra específica ou editar os usuários permitidos.

8. Depois de fazer alterações em uma política de **controles de aplicativo adaptável** , clique em **salvar**.

### <a name="not-recommended-list"></a>Lista de não recomendadas

A central de segurança só recomenda políticas de lista de permissões de aplicativos para máquinas virtuais que executam um conjunto estável de aplicativos. Não são criadas recomendações se as aplicações nas VMs associadas forem constantemente alteradas.

![Recomendação](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

A lista contém:
- **Nome do grupo**: o nome da assinatura e do grupo
- **VMs e computadores**: o número de máquinas virtuais no grupo

A central de segurança do Azure permite que você defina uma política de lista de permissões de aplicativo em grupos de VMs não recomendados. Siga os mesmos princípios descritos anteriormente, para configurar uma política de lista de permissões de aplicativo nesses grupos também.

## <a name="move-a-vm-from-one-group-to-another"></a>Mover uma VM de um grupo para outro

 Quando você move uma VM de um grupo para outro, a política de controle de aplicativo aplicada a ela muda para as configurações do grupo para o qual você o moveu. Você também pode mover uma VM de um grupo configurado para um grupo não configurado, o que resulta na remoção de qualquer política de controle de aplicativo aplicada anteriormente à VM.

 1. Na página **controles de aplicativo adaptáveis** , na guia **configurada** , clique no grupo ao qual a VM que será movida pertence atualmente.
1. Clique em **VMs e computadores configurados**.
1. Clique nos três pontos na linha da VM para mover e clique em **mover**. A janela **mover computador para um grupo diferente** é aberta.

    ![Proteção](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. Selecione o grupo para o qual mover a VM e clique em **mover computador**e clique em **salvar**.

    ![Proteção](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> Certifique-se de clicar em **salvar** depois de clicar em **mover computador**. Se você não clicar em **salvar**, o computador não será movido.

## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a usar o controle de aplicativo adaptável na central de segurança do Azure para aplicativos de lista de permissões em execução no Azure e em VMs não Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerir alertas e responder a incidentes de segurança no Centro de Segurança.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Compreender os alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de Resolução de Problemas do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como resolver problemas comuns no Centro de Segurança.
* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
