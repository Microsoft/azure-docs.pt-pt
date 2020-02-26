---
title: Controlos de aplicações adaptáveis no Centro de Segurança do Azure
description: Este documento ajuda-o a utilizar o controlo adaptativo de aplicações no Azure Security Center para aplicações whitelist em funcionamento em máquinas Azure.
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
ms.openlocfilehash: 1dc94c5ec08cc27fb1819ccc16fd766c62aad796
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604669"
---
# <a name="adaptive-application-controls"></a>Controlos de aplicações adaptáveis
Saiba como configurar o controlo de aplicações no Centro de Segurança do Azure com estas instruções.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>O que são os controlos de aplicações adaptativos do Centro de Segurança?
O controlo adaptativo da aplicação é uma solução inteligente, automatizada e de ponta a ponta do Azure Security Center, que o ajuda a controlar quais as aplicações que podem ser executadas nas suas máquinas Azure e não-Azure (Windows e Linux). Entre outros benefícios, isto ajuda a endurecer as suas máquinas contra malware. O Security Center utiliza machine learning para analisar as aplicações em execução nas suas máquinas e cria uma lista de permitir a partir desta inteligência. Esta capacidade simplifica consideravelmente o processo de configuração e manutenção da aplicação permite políticas de listas, permitindo-lhe:

- Bloqueie ou alerte sobre as tentativas de executar aplicações maliciosas, incluindo aquelas que de outra forma poderiam ser perdidas por soluções antimalware.
- Estar em conformidade com a política de segurança da sua organização que estabelece que só pode ser utilizado software licenciado.
- Evitar a utilização de software não desejado no seu ambiente.
- Evitar a execução de aplicações antigas e não suportadas.
- Impedir ferramentas de software específicas que não são permitidas na sua organização.
- Permitir que a equipa de TI controle o acesso a dados confidenciais através da utilização de aplicações.

> [!NOTE]
> No caso das máquinas Não Azure e Linux, os controlos de aplicações adaptantes são suportados apenas no modo de auditoria.

## <a name="how-to-enable-adaptive-application-controls"></a>Como ativar os controlos de aplicações adaptáveis?

Os controlos de aplicação adaptativo ajudam-no a definir um conjunto de aplicações que são autorizadas a funcionar em grupos configurados de máquinas. Esta funcionalidade está disponível tanto para o Azure como para o Windows não-Azure (todas as versões, clássicos ou Azure Resource Manager) e para as máquinas Linux. Utilize os seguintes passos para configurar a sua aplicação permitir listas:

1. Abra o dashboard **Centro de Segurança**.

1. No painel esquerdo, selecione **Controlos de aplicação adaptáveis** localizados em **Defesa da cloud avançada**.

    [Defesa ![](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png#lightbox)

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

1. Selecione o separador **Recomendado** para uma lista de grupos com recomendações de controlo de aplicações:

   ![Recomendado](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   A lista inclui:

   - **Nome do Grupo**: O nome da subscrição e do grupo
   - **VMs e Computadores**: O número de máquinas virtuais no grupo
   - **Estado**: o estado das recomendações
   - **Gravidade**: o nível de gravidade das recomendações

2. Clique num grupo para abrir a opção de regras de controlo de **aplicações Create.**

   [![regras de controlo de aplicações](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png#lightbox)

3. Nos **VMs Selecionados,** reveja a lista de VMs recomendados e desmarque qualquer um a que não pretenda aplicar uma política de whitelisting de aplicações. Em seguida, verá duas listas:

   - **Aplicações recomendadas**: uma lista de aplicações frequentes nos VMs dentro deste grupo, e recomenda-se que sejam autorizadas a executar.
   - **Mais aplicações**: uma lista de aplicações menos frequentes nos VMs dentro deste grupo ou que são conhecidas como Exploráveis (ver mais abaixo), e recomendadas para revisão.

4. Reveja as aplicações em cada lista e desmarque as que não pretende aplicar. Cada lista inclui:

   - **NOME**: a informação do certificado ou o percurso completo de um pedido
   - **TIPOS DE FICHEIRO**: o tipo de ficheiro da aplicação. Isto pode ser EXE, Script, MSI ou qualquer permutação destetipo.
   - **EXPLOREABLE**: um ícone de aviso indica se uma aplicação específica pode ser usada por um intruso para contornar uma lista de aplicações permitidas. Recomenda-se que reveja estas aplicações antes da respetiva aprovação.
   - **UTILIZADORES**: os utilizadores que são recomendados para permissão de execução de uma aplicação

5. Depois de concluir as suas seleções, selecione **Criar**. <br>
   Depois de selecionar Create, o Azure Security Center cria automaticamente as regras adequadas para além da aplicação incorporada que permite a solução de lista disponível nos servidores do Windows (AppLocker).

> [!NOTE]
> - O Centro de Segurança precisa de um mínimo de duas semanas de dados para criar uma linha de base e preencher as recomendações exclusivas por grupo de VMs. Os novos clientes do escalão standard do Centro de Segurança devem esperar um comportamento em que, primeiro, os grupos de VMs são apresentados no separador *nenhuma recomendação*.
> - Os Controlos de Aplicações Adaptáveis do Centro de Segurança não suportam VMs para as quais já esteja ativada uma política do AppLocker por um GPO ou uma política de segurança local.
> -  Como uma boa prática de segurança, o Security Center tentará sempre criar uma regra de editor para aplicações que são selecionadas para serem permitidas, e apenas se uma aplicação não tiver informações de editor (aka não assinado), será criada uma regra de caminho para o caminho completo do aplicação específica.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Editar e monitorizar grupos configurados com o controlo de aplicações

1. Para editar e monitorizar um grupo configurado com uma política de aplicação permitir lista, volte à página de controlos de **aplicações adaptáveis** e selecione **configurado** em **Grupos de VMs:**

   ![Grupos](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   A lista inclui:

   - **Nome do Grupo**: o nome da subscrição e do grupo
   - **VMs e Computadores**: o número de máquinas virtuais no grupo
   - **Modo**: O modo de auditoria irá registar tentativas de executar aplicações que não estão na lista de autorizações; A aplicação não permitirá que as candidaturas sejam executadas a menos que estejam na lista de autorizações
   - **Alertas**: quaisquer violações atuais

2. Clique num grupo para fazer alterações na página de política de controlo de **aplicações Editar.**

   ![Proteção](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. No **Modo de proteção**, tem a possibilidade de selecionar entre o seguinte:

   - **Auditoria**: neste modo, a solução de controlo de aplicações não impõe as regras e realiza apenas a auditoria à atividade nas VMs protegidas. Esta opção é recomendada para cenários nos quais pretende primeiro observar o comportamento geral antes de bloquear uma aplicação para ser executada na VM de destino.
   - **Impor**: neste modo, a solução de controlo de aplicações impõe as regras e certifica-se de que as aplicações que não têm autorização para ser executadas são bloqueadas.

   > [!NOTE]
   > -  O modo de proteção **de aplicação da lei** é desativado até novo aviso.
   > - Conforme mencionado anteriormente, por predefinição, é sempre configurada uma política de controlo de aplicações nova no modo *Auditoria*. 
   >

4. No âmbito da **extensão política,** adicione qualquer caminho de candidatura que pretenda permitir. Depois de adicionar estes caminhos, o Security Center atualiza a aplicação que permite a política de listas nos VMs dentro do grupo selecionado de VMS e cria as regras adequadas para estas aplicações, além das regras que já estão em vigor.

5. Reveja as violações atuais listadas na secção de **alertas recentes.** Clique em cada linha para ser redirecionado para a página **de Alertas** dentro do Centro de Segurança Azure, e veja todos os alertas que foram detetados pelo Azure Security Center nos VMs associados.
   - **Alertas**: quaisquer violações que tenham sido registadas.
   - **Não, não, não, não. de VMs**: o número de máquinas virtuais com este tipo de alerta.

6. De acordo com **as regras da Editora whitelisting**, Path **whitelisting rules**, e **Hash whitelisting rules,** você pode ver quais as regras de whitelisting de aplicação que estão atualmente configuradas nos VMs dentro de um grupo, de acordo com o tipo de coleção de regras. Para cada regra pode ver:

   - **Regra**: Os parâmetros específicos segundo os quais uma aplicação é examinada pelo AppLocker para determinar se uma aplicação é permitida a execução.
   - **Tipo**de ficheiro : Os tipos de ficheiros que estão cobertos por uma regra específica. Isto pode ser qualquer um dos seguintes: EXE, Script, MSI ou qualquer permutação desses tipos de ficheiros.
   - **Utilizadores**: Nome ou número de utilizadores autorizados a executar uma aplicação que esteja abrangida por uma regra de listagem de listagem de aplicações.

   ![Regras de inclusão em listas de permissões](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Clique nos três pontos no final de cada linha se pretender eliminar a regra específica ou editar os utilizadores permitidos.

8. Depois de efazer alterações numa política de controlo de **aplicações adaptivas,** clique em **Guardar**.

### <a name="not-recommended-list"></a>Lista de não recomendadas

O Security Center apenas recomenda políticas de listagem de aplicações para máquinas virtuais que executem um conjunto estável de aplicações. Não são criadas recomendações se as aplicações nas VMs associadas forem constantemente alteradas.

![Recomendação](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

A lista contém:
- **Nome do Grupo**: o nome da subscrição e do grupo
- **VMs e Computadores**: o número de máquinas virtuais no grupo

O Azure Security Center permite-lhe definir uma política de whitelisting de aplicações em grupos não recomendados de VMs também. Siga os mesmos princípios que foram descritos anteriormente, para configurar uma política de whitelisting de aplicação também nesses grupos.

## <a name="move-a-vm-from-one-group-to-another"></a>Mover um VM de um grupo para outro

 Ao mover um VM de um grupo para outro, a política de controlo de aplicações aplicada às definições do grupo para a qual o mudou. Também pode mover um VM de um grupo configurado para um grupo não configurado, o que resulta na remoção de qualquer política de controlo de aplicações que tenha sido anteriormente aplicada ao VM.

 1. A partir da página de controlos de **aplicação adaptável,** a partir do separador **CONFIGURADO,** clique no grupo a que o VM a deslocar pertence atualmente.
1. Clique em **VMs e Computadores Configurados**.
1. Clique nos três pontos na linha do VM para mover-se e clicar em **Mover**. O **computador Move para diferentes** janelas de grupo abre- se.

    ![Proteção](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. Selecione o grupo para mover o VM e clique em **Mover Computador,** e clique em **Guardar**.

    ![Proteção](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> Certifique-se de clicar em **Guardar** depois de clicar em **Mover o computador**. Se não clicar em **Guardar,** o computador não será movido.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a utilizar o controlo adaptativo de aplicações no Azure Security Center para aplicações whitelist em execução em VMs Azure e não-Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerir alertas e responder a incidentes de segurança no Centro de Segurança.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Compreender os alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de Resolução de Problemas do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como resolver problemas comuns no Centro de Segurança.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
