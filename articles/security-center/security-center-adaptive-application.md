---
title: Controlos de aplicações adaptáveis no Centro de Segurança do Azure
description: Este documento ajuda-o a utilizar o controlo de aplicações adaptativas no Azure Security Center para aplicações whitelist em funcionamento em máquinas Azure.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77604669"
---
# <a name="adaptive-application-controls"></a>Controlos de aplicações adaptáveis
Saiba como configurar o controlo de aplicações no Centro de Segurança do Azure com estas instruções.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>O que são os controlos de aplicações adaptativos do Centro de Segurança?
O controlo de aplicações adaptativas é uma solução inteligente, automatizada e de ponta a ponta do Azure Security Center, que o ajuda a controlar quais aplicações podem ser executadas nas suas máquinas Azure e não-Azure (Windows e Linux). Entre outros benefícios, isto ajuda a endurecer as suas máquinas contra malware. O Security Center utiliza machine learning para analisar as aplicações em execução nas suas máquinas e cria uma lista de permitis a partir desta inteligência. Esta capacidade simplifica muito o processo de configuração e manutenção da aplicação, permitindo políticas de listas, permitindo-lhe:

- Bloqueie ou alerte para tentativas de executar aplicações maliciosas, incluindo aquelas que de outra forma poderiam ser perdidas por soluções antimalware.
- Estar em conformidade com a política de segurança da sua organização que estabelece que só pode ser utilizado software licenciado.
- Evitar a utilização de software não desejado no seu ambiente.
- Evitar a execução de aplicações antigas e não suportadas.
- Impedir ferramentas de software específicas que não são permitidas na sua organização.
- Permitir que a equipa de TI controle o acesso a dados confidenciais através da utilização de aplicações.

> [!NOTE]
> Para máquinas não-Azure e Linux, os controlos de aplicação adaptativa são suportados apenas no modo de auditoria.

## <a name="how-to-enable-adaptive-application-controls"></a>Como ativar os controlos de aplicações adaptáveis?

Os controlos de aplicação adaptativos ajudam-no a definir um conjunto de aplicações que podem ser executadas em grupos de máquinas configurados. Esta funcionalidade encontra-se disponível tanto para as máquinas Azure como para o Windows não-Azure (todas as versões, classic ou Azure Resource Manager) e linux. Utilize os seguintes passos para configurar as listas de admissões:

1. Abra o dashboard **Centro de Segurança**.

1. No painel esquerdo, selecione **Controlos de aplicação adaptáveis** localizados em **Defesa da cloud avançada**.

    [![Defesa](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png#lightbox)

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

1. Selecione o **separador Recomendado** para uma lista de grupos com recomendações de controlo de aplicações:

   ![Recomendado](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   A lista inclui:

   - **Nome do grupo**: O nome da subscrição e do grupo
   - **VMs e Computadores**: O número de máquinas virtuais no grupo
   - **Estado**: o estado das recomendações
   - **Gravidade**: o nível de gravidade das recomendações

2. Clique num grupo para abrir a opção **Criar regras de controlo de aplicações.**

   [![Regras de controlo de aplicações](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png#lightbox)

3. Nos **VMs Select,** reveja a lista de VMs recomendados e desmarque qualquer um a que não queira aplicar uma política de whitelisting de aplicação. Em seguida, verá duas listas:

   - **Aplicações recomendadas**: uma lista de aplicações frequentes nos VMs dentro deste grupo, e são recomendadas para serem executadas.
   - **Mais aplicações**: uma lista de aplicações menos frequentes nos VMs dentro deste grupo ou que são conhecidas como Exploráveis (ver mais abaixo) e recomendadas para revisão.

4. Reveja as aplicações em cada lista e desmarque as que não pretende aplicar. Cada lista inclui:

   - **NOME**: as informações do certificado ou o percurso completo de um pedido
   - **TIPOS DE FICHEIRO**: o tipo de ficheiro da aplicação. Isto pode ser EXE, Script, MSI ou qualquer permutação destes tipos.
   - **EXPLOITABLE**: um ícone de aviso indica se uma aplicação específica pode ser usada por um intruso para contornar uma lista de autorização de aplicação. Recomenda-se que reveja estas aplicações antes da respetiva aprovação.
   - **UTILIZADORES**: os utilizadores que são recomendados para permissão de execução de uma aplicação

5. Depois de concluir as suas seleções, selecione **Criar**. <br>
   Depois de selecionar Criar, o Azure Security Center cria automaticamente as regras adequadas em cima da solução de lista de pedidos incorporados disponíveis nos servidores windows (AppLocker).

> [!NOTE]
> - O Centro de Segurança precisa de um mínimo de duas semanas de dados para criar uma linha de base e preencher as recomendações exclusivas por grupo de VMs. Os novos clientes do escalão standard do Centro de Segurança devem esperar um comportamento em que, primeiro, os grupos de VMs são apresentados no separador *nenhuma recomendação*.
> - Os Controlos de Aplicações Adaptáveis do Centro de Segurança não suportam VMs para as quais já esteja ativada uma política do AppLocker por um GPO ou uma política de segurança local.
> -  Como uma boa prática de segurança, o Security Center tentará sempre criar uma regra de editor para aplicações que são selecionadas para serem permitidas, e apenas se uma aplicação não tiver uma informação de editor (aka não assinada), será criada uma regra de caminho para o caminho completo da aplicação específica.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Editar e monitorizar grupos configurados com o controlo de aplicações

1. Para editar e monitorizar um grupo configurado com uma política de lista de pedidos, volte à página **de controlos de aplicações adaptativas** e selecione **CONFIGURED** em **grupos de VMs**:

   ![Grupos](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   A lista inclui:

   - **Nome do grupo**: o nome da subscrição e do grupo
   - **VMs e Computadores**: o número de máquinas virtuais no grupo
   - **Modo**: O modo de auditoria registará tentativas de executar aplicações que não constam da lista de autorizações; A aplicação não permitirá que as aplicações sejam executadas a menos que estejam na lista de autorizações
   - **Alertas**: quaisquer violações atuais

2. Clique num grupo para escoar alterações na página de política de controlo de **aplicações editar.**

   ![Proteção](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. No **Modo de proteção**, tem a possibilidade de selecionar entre o seguinte:

   - **Auditoria**: neste modo, a solução de controlo de aplicações não impõe as regras e realiza apenas a auditoria à atividade nas VMs protegidas. Esta opção é recomendada para cenários nos quais pretende primeiro observar o comportamento geral antes de bloquear uma aplicação para ser executada na VM de destino.
   - **Impor**: neste modo, a solução de controlo de aplicações impõe as regras e certifica-se de que as aplicações que não têm autorização para ser executadas são bloqueadas.

   > [!NOTE]
   > -  **O** modo de proteção é desativado até novo aviso.
   > - Conforme mencionado anteriormente, por predefinição, é sempre configurada uma política de controlo de aplicações nova no modo *Auditoria*. 
   >

4. Na **extensão de Política,** adicione qualquer caminho de aplicação que pretenda permitir. Depois de adicionar estes caminhos, o Security Center atualiza a aplicação permite a política de listas nos VMs dentro do grupo selecionado de VMS e cria as regras adequadas para estas aplicações, além das regras que já estão em vigor.

5. Reveja as violações atuais listadas na secção **de alertas recentes.** Clique em cada linha para ser redirecionado para a página **alertas** dentro do Centro de Segurança Azure e veja todos os alertas que foram detetados pelo Azure Security Center nos VMs associados.
   - **Alertas:** quaisquer violações registadas.
   - **Não, não, não, não. de VMs:** o número de máquinas virtuais com este tipo de alerta.

6. De acordo com **as regras de whitelisting da Publisher**, **regras de whitelisting Path**e **hash whitelisting,** pode ver quais as regras de whitelisting de aplicações que estão atualmente configuradas nos VMs dentro de um grupo, de acordo com o tipo de recolha de regras. Para cada regra pode ver:

   - **Regra**: Os parâmetros específicos segundo os quais uma aplicação é examinada pelo AppLocker para determinar se uma aplicação é permitida a ser executada.
   - **Tipo de ficheiro**: Os tipos de ficheiros abrangidos por uma regra específica. Isto pode ser qualquer um dos seguintes: EXE, Script, MSI ou qualquer permutação desses tipos de ficheiros.
   - **Utilizadores**: Nome ou número de utilizadores que estão autorizados a executar uma aplicação abrangida por uma regra de whitelisting de aplicações.

   ![Regras de inclusão em listas de permissões](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Clique nos três pontos no final de cada linha se quiser eliminar a regra específica ou editar os utilizadores autorizados.

8. Depois de escoar as alterações a uma política **de controlo de aplicações adaptativas,** clique em **Guardar**.

### <a name="not-recommended-list"></a>Lista de não recomendadas

O Security Center apenas recomenda políticas de whitelisting de aplicações para máquinas virtuais que executam um conjunto estável de aplicações. Não são criadas recomendações se as aplicações nas VMs associadas forem constantemente alteradas.

![Recomendação](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

A lista contém:
- **Nome do grupo**: o nome da subscrição e do grupo
- **VMs e Computadores**: o número de máquinas virtuais no grupo

O Azure Security Center permite-lhe definir uma política de whitelisting de aplicações em grupos não recomendados de VMs também. Siga os mesmos princípios descritos anteriormente, para configurar também uma política de listagem de aplicações para esses grupos.

## <a name="move-a-vm-from-one-group-to-another"></a>Mover um VM de um grupo para outro

 Quando se move um VM de um grupo para outro, a política de controlo de aplicações aplicada a ela altera-se às definições do grupo para o qual o transferiu. Também pode mover um VM de um grupo configurado para um grupo não configurado, o que resulta na remoção de qualquer política de controlo de aplicações que tenha sido previamente aplicada ao VM.

 1. A partir da página de controlos de **aplicação adaptável,** a partir do separador **CONFIGURED,** clique no grupo a que o VM a ser movido atualmente pertence.
1. Clique **em VMs e Computadores configurados.**
1. Clique nos três pontos na linha do VM para se mover e clicar em **Mover.** O **computador Move para diferentes** janelas de grupo abre-se.

    ![Proteção](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. Selecione o grupo para mover o VM e clique em **"Move Computer"** e clique em **Guardar**.

    ![Proteção](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> Certifique-se de clicar **em Guardar** depois de clicar no **Move Computer**. Se não clicar em **Guardar,** então o computador não será movido.

## <a name="next-steps"></a>Próximos passos
Neste documento, aprendeu a utilizar o controlo de aplicações adaptativas no Azure Security Center para aplicações whitelist em execução em VMs Azure e não-Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Gerir e responder aos alertas de segurança no Centro de Segurança Azure.](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) Saiba como gerir alertas e responder a incidentes de segurança no Centro de Segurança.
* [Monitorização de saúde de segurança no Centro de Segurança Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Compreender os alertas de segurança no Centro de Segurança Azure.](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de resolução de problemas do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como resolver problemas comuns no Centro de Segurança.
* [Blog de Segurança Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
