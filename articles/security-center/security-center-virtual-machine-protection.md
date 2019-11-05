---
title: Protegendo seus computadores e aplicativos na central de segurança do Azure | Microsoft Docs
description: Este documento aborda as recomendações na central de segurança que ajudam a proteger suas máquinas virtuais e computadores e seus aplicativos Web e ambientes de serviço de aplicativo.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 2317d0206e3fc4a342606d17c172ac42cbe82870
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520680"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Protegendo seus computadores e aplicativos na central de segurança do Azure
A central de segurança do Azure analisa o estado de segurança de seus recursos do Azure, servidores não Azure e máquinas virtuais. Quando a central de segurança identifica possíveis vulnerabilidades de segurança, ele cria recomendações que o orientam durante o processo de configuração dos controles necessários. As recomendações se aplicam aos tipos de recursos do Azure: máquinas virtuais (VMs) e computadores, aplicativos, rede, SQL e identidade e acesso.

Este artigo aborda as recomendações que se aplicam a computadores e aplicativos.

## <a name="monitoring-security-health"></a>Monitorizar o estado de funcionamento de segurança
Você pode monitorar o estado de segurança de seus recursos na **central de segurança – painel Visão geral** . A seção **recursos** fornece o número de problemas identificados e o estado de segurança para cada tipo de recurso.

Você pode exibir uma lista de todos os problemas selecionando **recomendações**. Para obter mais informações sobre como aplicar recomendações, consulte [implementando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md).

Para obter uma lista completa de recomendações de serviços de computação e de aplicativo, consulte [recomendações](security-center-virtual-machine-protection.md#compute-and-app-recs).

Para continuar, selecione **computação & aplicativos** em **recursos** ou no menu principal da central de segurança.
![painel da central de segurança](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>Monitorar computação e serviços de aplicativos
Em **computação & aplicativos**, há as seguintes guias:

- **Descrição geral**: monitorização e recomendações identificadas pelo Centro de Segurança.
- **VMs e computadores**: lista das suas VMS, computadores, máquinas virtuais e os atuais estados de segurança de cada um.
- **Serviços Cloud**: lista das suas funções de trabalho e na Web monitorizadas pelo Centro de Segurança.
- **Serviços de aplicativos**: lista de seus ambientes de serviço de aplicativo e o estado de segurança atual de cada um.
- **Contêineres**: lista de seus contêineres e avaliação de segurança de suas configurações.
- **Recursos de computação (versão prévia)** : lista de recomendações para seus recursos de computação, como clusters de Service Fabric e hubs de eventos.

Para continuar, selecione **computação & aplicativos** em **higiene de segurança de recurso**.

![Computação](./media/security-center-virtual-machine-recommendations/compute.png)

Existem várias secções em cada separador e pode selecionar, em cada uma delas, uma opção individual para ver mais detalhes sobre os passos recomendados para resolver esse problema em concreto.

### Máquinas virtuais e computadores não monitorados<a name="unmonitored-vms-and-computers"></a>
Uma VM ou um computador não será monitorado pela central de segurança se o computador não estiver executando a extensão de Microsoft Monitoring Agent. Um computador pode ter um agente local já instalado, por exemplo, o agente direto do OMS ou o agente de System Center Operations Manager. Computadores com esses agentes são identificados como não monitorados porque não há suporte total para esses agentes na central de segurança. Para beneficiar totalmente de todas as capacidades do Centro de Segurança, é necessária a extensão MMA.

Você pode instalar a extensão na VM ou no computador não monitorado, além do agente local já instalado. Configure ambos os agentes da mesma forma, ligando-os à mesma área de trabalho. Isto permite ao Centro de Segurança interagir com a extensão MMA e recolher dados. Consulte [Ativar a extensão VM](../azure-monitor/learn/quick-collect-azurevm.md) para obter instruções sobre como instalar a extensão MMA.

Consulte [problemas de estado do agente de monitorização](security-center-troubleshooting-guide.md#mon-agent) para saber mais sobre os motivos pelos quais o Centro de Segurança não consegue monitorizar com êxito VMs e computadores inicializados para aprovisionamento automático.

### <a name="recommendations"></a>Recomendações
Esta seção tem um conjunto de recomendações para cada VM e computador, funções da Web e de trabalho, Azure App aplicativos Web do serviço e Ambiente do Serviço de Aplicativo do Azure que a central de segurança monitora. A primeira coluna indica a recomendação. A segunda coluna mostra o número total de recursos afetados por essa recomendação. A terceira coluna mostra a gravidade do problema.

Cada recomendação tem um conjunto de ações que você pode executar depois de selecioná-la. Por exemplo, se você selecionar **atualizações do sistema ausentes**, o número de VMs e computadores com patches ausentes e a severidade da atualização ausente será exibida.

A **aplicação de atualizações do sistema** tem um resumo das atualizações críticas em um formato de grafo, uma para o Windows e outra para o Linux. A segunda parte tem uma tabela com as seguintes informações:

- **NOME**: o nome da atualização em falta.
- **Não. DE VMs & computadores**: número total de VMs e computadores sem esta atualização.
- **Gravidade da atualização**: descreve a severidade dessa recomendação específica:

    - **Crítico**: existe uma vulnerabilidade com um recurso significativo (aplicativo, máquina virtual ou grupo de segurança de rede) e requer atenção.
    - **Importante**: etapas não críticas ou adicionais são necessárias para concluir um processo ou eliminar uma vulnerabilidade.
    - **Moderado**: uma vulnerabilidade deve ser resolvida, mas não requer atenção imediata. (Por predefinição, as recomendações baixas não são apresentadas, mas pode filtrar por recomendações baixas se pretender visualizá-las).


- **ESTADO**: o estado atual da recomendação:

    - **Aberto**: a recomendação ainda não foi tratada.
    - **Em curso**: a recomendação está a ser aplicada a esses recursos e não tem de tomar qualquer medida.
    - **Resolvido**: a recomendação já foi concluída. (Quando o problema for resolvido, a entrada fica a cinzento).

Para ver os detalhes das recomendações, clique no nome da atualização em falta na lista.


> [!NOTE]
> As recomendações de segurança aqui são as mesmas no bloco **recomendações** . Para obter mais informações sobre como resolver recomendações, consulte [implementando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md).
>
>

### <a name="vms-and-computers"></a>VMs e computadores
A seção VMs e computadores fornece uma visão geral de todas as recomendações da VM e do computador. Cada coluna representa um conjunto de recomendações.

![Recomendações da VM e do computador](./media/security-center-virtual-machine-recommendations/vm-computers.png)

Há quatro tipos de ícones representados nesta lista:

![Computador não pertencente ao Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Computador não Azure.

![VM Azure Resource Manager](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager VM.

![VM clássica do Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) VM clássica do Azure.


![VMs identificadas no espaço de trabalho](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) VMs que só são identificadas a partir da área de trabalho que faz parte da subscrição vista. Isso inclui VMs de outras assinaturas que são relatadas para o espaço de trabalho nessa assinatura e VMs que foram instaladas com o Operations Manager agente direto e não têm nenhuma ID de recurso.

O ícone que aparece em cada recomendação ajuda a identificar rapidamente a VM e o computador que precisam de atenção e o tipo de recomendação. Você também pode usar os filtros para pesquisar a lista por **tipo de recurso** e por **severidade**.

Para fazer uma busca detalhada nas recomendações de segurança para cada VM, clique na VM.
Aqui você verá os detalhes de segurança para a VM ou o computador. Na parte inferior, você pode ver a ação recomendada e a gravidade de cada problema.
![Serviços cloud](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Serviços em nuvem
Para serviços de nuvem, uma recomendação é criada quando a versão do sistema operacional está desatualizada.

![Serviços em nuvem](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Em um cenário em que você tem uma recomendação (que não é o caso do exemplo anterior), você precisa seguir as etapas na recomendação para atualizar a versão do sistema operacional. Quando está disponível uma atualização, verá um alerta (vermelho ou cor de laranja, dependendo da gravidade do problema). Quando você seleciona esse alerta nas linhas WebRole1 (executa o Windows Server com seu aplicativo Web automaticamente implantado no IIS) ou WorkerRole1 (executa o Windows Server com seu aplicativo Web automaticamente implantado no IIS), você vê mais detalhes sobre essa recomendação.

Para ver uma explicação mais prescritiva sobre esta recomendação, clique em **Atualizar versão do SO** na coluna **DESCRIÇÃO**.



![Atualizar a versão do SO](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services"></a>Serviços aplicacionais
Para exibir as informações do serviço de aplicativo, você deve habilitar o serviço de aplicativo em sua assinatura. Para obter instruções sobre como habilitar esse recurso, consulte [proteger o serviço de aplicativo com a central de segurança do Azure](security-center-app-services.md).
[!NOTE]
> O monitoramento do serviço de aplicativo está em visualização e está disponível apenas na camada Standard da central de segurança.


Em **serviços de aplicativos**, você encontra uma lista de seus ambientes de serviço de aplicativo e o resumo de integridade com base na central de segurança de avaliação executada.

![Serviços aplicacionais](./media/security-center-virtual-machine-recommendations/app-services.png)

Há três tipos de ícones representados nesta lista:

![Ambiente de serviços de aplicativos](./media/security-center-virtual-machine-recommendations/ase.png) Ambiente de serviços de aplicativos.

![Aplicação Web](./media/security-center-virtual-machine-recommendations/web-app.png) Aplicativo Web.

![Aplicativo de funções](./media/security-center-virtual-machine-recommendations/function-app.png) Aplicativo de funções.

1. Selecione um aplicativo Web. Uma exibição de resumo é aberta com três guias:

   - **Recomendações**: com base em avaliações executadas pela central de segurança que falharam.
   - **Avaliações passadas**: lista de avaliações executadas pela central de segurança que passaram.
   - **Avaliações indisponíveis**: lista de avaliações que não foram executadas devido a um erro ou a recomendação não é relevante para o serviço de aplicativo específico

   Em **recomendações** é uma lista das recomendações para o aplicativo Web selecionado e a severidade de cada recomendação.

   ![Recomendações de serviços de aplicativos](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. Selecione uma recomendação para ver uma descrição da recomendação e uma lista de recursos não íntegros, recursos íntegros e recursos não verificados.

   - Na coluna **avaliações passadas** , há uma lista de avaliações aprovadas.  A severidade dessas avaliações é sempre verde.

   - Selecione uma avaliação aprovada na lista para obter uma descrição da avaliação, uma lista de recursos não íntegros e íntegros e uma lista de recursos não verificados. Há uma guia para recursos não íntegros, mas essa lista está sempre vazia desde a avaliação aprovada.

     ![Correção do serviço de aplicativo](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)

## <a name="virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquinas virtuais
A central de segurança descobre automaticamente se você tem conjuntos de escala e recomenda que você instale o Microsoft Monitoring Agent nesses conjuntos de dimensionamento. 

Para instalar o Microsoft Monitoring Agent: 

1. Selecione a recomendação **instalar o agente de monitoramento no conjunto de dimensionamento de máquinas virtuais**. Você Obtém uma lista de conjuntos de dimensionamento não monitorados.
2. Selecione um conjunto de dimensionamento não íntegro. Siga as instruções para instalar o agente de monitoramento usando um espaço de trabalho preenchido existente ou crie um novo. Certifique-se de definir o [tipo de preço](security-center-pricing.md) do espaço de trabalho se ele não estiver definido.

   ![Instalar o MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Para definir novos conjuntos de dimensionamento para instalar automaticamente o Microsoft Monitoring Agent:
1. Vá para Azure Policy e clique em **definições**.
2. Procure a política **implantar log Analytics agente para conjuntos de dimensionamento de máquinas virtuais do Windows** e clique nele.
3. Clique em **Atribuir**.
4. Defina o **escopo** e **log Analytics espaço de trabalho** e clique em **atribuir**.

Se você quiser definir todos os conjuntos de dimensionamento existentes para instalar o Microsoft Monitoring Agent, em Azure Policy, vá para **correção** e aplique a política existente aos conjuntos de dimensionamento existentes.


## Recomendações de computação e aplicativo<a name="compute-and-app-recs"></a>
|Tipo de recurso|Classificação de segurança|Recomendação|Descrição|
|----|----|----|----|
|Serviço de aplicativo|20|O aplicativo Web só deve ser acessível via HTTPS|Limite o acesso de aplicativos Web somente por HTTPS.|
|Serviço de aplicativo|20|Aplicativo de funções só deve ser acessível via HTTPS|Limite o acesso de aplicativos de funções somente por HTTPS.|
|Serviço de aplicativo|5|Os logs de diagnóstico nos serviços de aplicativos devem ser habilitados|Habilite os logs e mantenha-os em um ano. Isso permite que você recrie trilhas de atividade para fins de investigação quando ocorrer um incidente de segurança ou se sua rede estiver comprometida. |
|Serviço de aplicativo|10|A depuração remota deve ser desativada para o aplicativo Web|Desative a depuração de aplicativos Web se você não precisar mais usá-lo. A depuração remota requer que as portas de entrada sejam abertas em um Aplicativo de funções.|
|Serviço de aplicativo|10|A depuração remota deve ser desativada para o aplicativo de funções|Desative a depuração para Aplicativo de funções se você não precisar mais usá-la. A depuração remota requer que as portas de entrada sejam abertas em um Aplicativo de funções.|
|Serviço de aplicativo|10|Não permitir que todos os recursos (' * ') acessem seu aplicativo| Não permita o conjunto do parâmetro WEBSITE_LOAD_CERTIFICATES como "". Definir o parâmetro como ' ' significa que todos os certificados são carregados no repositório de certificados pessoal de seus aplicativos Web. Isso pode levar ao abuso do princípio de menos privilégios, pois é improvável que o site precise de acesso a todos os certificados em tempo de execução.|
|Serviço de aplicativo|20|O CORS não deve permitir que todos os recursos acessem seus aplicativos Web|Permitir que somente os domínios necessários interajam com seu aplicativo Web. O CORS (compartilhamento de recursos entre origens) não deve permitir que todos os domínios acessem seu aplicativo Web.|
|Serviço de aplicativo|20|O CORS não deve permitir que todos os recursos acessem seu Aplicativo de funções| Permitir que somente os domínios necessários interajam com seu aplicativo de funções. O CORS (compartilhamento de recursos entre origens) não deve permitir que todos os domínios acessem seu aplicativo de funções.|
|Recursos de computação (lote)|1|As regras de alerta de métrica devem ser configuradas em contas do lote|Configure as regras de alerta de métrica na conta do lote e habilite os eventos completos de exclusão do pool de métricas e eventos de início de exclusão de pool|
|Recursos de computação (Service Fabric)|10|Clusters de Service Fabric só devem usar Azure Active Directory para autenticação de cliente|Execute a autenticação de cliente somente por meio de Azure Active Directory no Service Fabric.|
|Recursos de computação (conta de automação)|5|As variáveis da conta de automação devem ser criptografadas|Habilite a criptografia de ativos de variável de conta de automação ao armazenar dados confidenciais.|
|Recursos de computação (pesquisa)|5|Habilitação de auditoria de logs de diagnóstico para serviços de pesquisa|Habilite os logs e mantenha-os em um ano. Isso permite que você recrie trilhas de atividade para fins de investigação quando ocorrer um incidente de segurança ou se sua rede estiver comprometida. |
|Recursos de computação (barramento de serviço)|5|Os logs de diagnóstico no barramento de serviço devem ser habilitados|Habilite os logs e mantenha-os em um ano. Isso permite que você recrie trilhas de atividade para fins de investigação quando ocorrer um incidente de segurança ou se sua rede estiver comprometida. |
|Recursos de computação (Stream Analytics)|5|Os logs de diagnóstico no Azure Stream Analytics devem ser habilitados|Habilite os logs e mantenha-os em um ano. Isso permite que você recrie trilhas de atividade para fins de investigação quando ocorrer um incidente de segurança ou se sua rede estiver comprometida. |
|Recursos de computação (lote)|5|Habilitar logs de diagnóstico em contas do lote|Habilite os logs e mantenha-os em um ano. Isso permite que você recrie trilhas de atividade para fins de investigação quando ocorrer um incidente de segurança ou se sua rede estiver comprometida. |
|Recursos de computação (Hub de eventos)|5|Os logs de diagnóstico no Hub de eventos devem ser habilitados|Habilite os logs e mantenha-os em um ano. Isso permite que você recrie trilhas de atividade para fins de investigação quando ocorrer um incidente de segurança ou se sua rede estiver comprometida. |
|Recursos de computação (aplicativos lógicos)|5|Habilitar logs de diagnóstico em aplicativos lógicos|Habilite os logs e mantenha-os em um ano. Isso permite que você recrie trilhas de atividade para fins de investigação quando ocorrer um incidente de segurança ou se sua rede estiver comprometida. |
|Recursos de computação (Service Fabric)|15|Defina a propriedade ClusterProtectionLevel como EncryptAndSign em Service Fabric|Service Fabric fornece três níveis de proteção (None, Sign e EncryptAndSign) para comunicação de nó para nó usando um certificado de cluster primário.  Defina o nível de proteção para garantir que todas as mensagens de nó para nó sejam criptografadas e assinadas digitalmente. |
|Recursos de computação (barramento de serviço)|1|Remover todas as regras de autorização, exceto RootManageSharedAccessKey do namespace do barramento de serviço |Os clientes do barramento de serviço não devem usar uma política de acesso no nível de namespace que forneça acesso a todas as filas e tópicos em um namespace. Para alinhar com o modelo de segurança de privilégios mínimos, você deve criar políticas de acesso no nível de entidade para filas e tópicos para fornecer acesso apenas à entidade específica.|
|Recursos de computação (Hub de eventos)|1|Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do namespace do hub de eventos|Os clientes do hub de eventos não devem usar uma política de acesso no nível de namespace que forneça acesso a todas as filas e tópicos em um namespace. Para alinhar com o modelo de segurança de privilégios mínimos, você deve criar políticas de acesso no nível de entidade para filas e tópicos para fornecer acesso apenas à entidade específica.|
|Recursos de computação (Hub de eventos)|5|As regras de autorização na entidade do hub de eventos devem ser definidas|Auditar regras de autorização na entidade do hub de eventos para conceder acesso com privilégios mínimos.|
|Máquina|50|Instalar o agente de monitoramento em seus computadores|Instale o agente de monitoramento para habilitar a coleta de dados, verificação de atualizações, verificação de linha de base e proteção de ponto de extremidade em cada computador.|
|Máquina|50|Habilitar provisionamento automático e coleta de dados para suas assinaturas |Habilite o provisionamento automático e a coleta de dados para computadores em suas assinaturas para habilitar a coleta de dados, verificação de atualizações, verificação de linha de base e proteção de ponto de extremidade em cada computador adicionado às suas assinaturas.|
|Máquina|40|Resolver problemas de integridade do agente de monitoramento em seus computadores|Para a proteção completa da central de segurança, resolva os problemas do agente de monitoramento em seus computadores seguindo as instruções no guia de solução de problemas| 
|Máquina|40|Resolver problemas de integridade do Endpoint Protection em seus computadores|Para a proteção completa da central de segurança, resolva os problemas do agente de monitoramento em seus computadores seguindo as instruções no guia de solução de problemas.|
|Máquina|40|Solucionar problemas de dados de verificação ausentes em seus computadores|Solucionar problemas de dados de verificação ausentes em máquinas virtuais e computadores. Os dados de verificação ausentes em seus computadores resultam em avaliações de segurança ausentes, como verificação de atualização, verificação de linha de base e verificação de solução ausente do Endpoint Protection.|
|Máquina|40|As atualizações do sistema devem ser instaladas em seus computadores|Instalar atualizações críticas e de segurança do sistema ausentes para proteger seus computadores e máquinas virtuais Windows e Linux
|Máquina|15|Adicionar uma firewall de aplicação Web| Implante uma solução de WAF (firewall do aplicativo Web) para proteger seus aplicativos Web. |
|Máquina|40|Atualizar a versão do sistema operacional para suas funções de serviço de nuvem|Atualize a versão do sistema operacional (SO) para suas funções de serviço de nuvem para a versão mais recente disponível para a família do sistema operacional.|
|Máquina|35|Vulnerabilidades na configuração de segurança em seus computadores devem ser corrigidas|Corrija vulnerabilidades na configuração de segurança em seus computadores para protegê-las contra ataques.|
|Máquina|35|Corrigir vulnerabilidades na configuração de segurança em seus contêineres|Corrija vulnerabilidades na configuração de segurança em computadores com o Docker instalado para protegê-los contra ataques.|
|Máquina|25|Habilitar controles de aplicativo adaptáveis|Habilite o controle de aplicativo para controlar quais aplicativos podem ser executados em suas VMs localizadas no Azure. Isso ajudará a proteger suas VMs contra malware. A central de segurança usa o aprendizado de máquina para analisar os aplicativos em execução em cada VM e ajuda a aplicar regras de permissão usando essa inteligência. Esse recurso simplifica o processo de configuração e manutenção de regras de permissão de aplicativo.|
|Máquina|20|Instalar a solução Endpoint Protection em seus computadores|Instale uma solução de proteção de ponto de extremidade em suas máquinas virtuais para protegê-las contra ameaças e vulnerabilidades.|
|Máquina|20|Reinicie os computadores para aplicar atualizações do sistema|Reinicie os computadores para aplicar as atualizações do sistema e proteger a máquina contra vulnerabilidades.|
|Máquina|15|A criptografia de disco deve ser aplicada em máquinas virtuais|Criptografe seus discos de máquina virtual usando Azure Disk Encryption para máquinas virtuais Windows e Linux. Azure Disk Encryption (ADE) aproveita o recurso de BitLocker padrão do setor do Windows e o recurso DM-cript do Linux para fornecer criptografia de disco do sistema operacional e de dados para ajudar a proteger e proteger seus dados e ajudar a atender à segurança e à conformidade da organização compromissos no cofre de chaves do Azure do cliente. Quando seu requisito de conformidade e segurança exigir que você criptografe os dados de ponta a ponta usando suas chaves de criptografia, incluindo a criptografia do disco efêmero (temporário anexado localmente), use o Azure Disk Encryption. Como alternativa, por padrão, os Managed Disks são criptografados em repouso por padrão usando o Azure Criptografia do Serviço de Armazenamento em que as chaves de criptografia são chaves gerenciadas pela Microsoft no Azure. Se isso atender aos seus requisitos de conformidade e de segurança, você poderá aproveitar a criptografia de disco gerenciado padrão para atender às suas necessidades.|
|Máquina|30|Instalar uma solução de avaliação de vulnerabilidade em suas máquinas virtuais|Instalar uma solução de avaliação de vulnerabilidade em suas máquinas virtuais|
|Máquina|15|Adicionar uma firewall de aplicação Web| Implante uma solução de WAF (firewall do aplicativo Web) para proteger seus aplicativos Web. |
|Máquina|30|Vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidade|As máquinas virtuais para as quais uma solução de avaliação de vulnerabilidades de terceiros são implantadas estão sendo continuamente avaliadas em relação a vulnerabilidades de aplicativo e sistema operacional. Sempre que essas vulnerabilidades forem encontradas, elas estarão disponíveis para obter mais informações como parte da recomendação.|
|Máquina|30|Instalar uma solução de avaliação de vulnerabilidade em suas máquinas virtuais|Instalar uma solução de avaliação de vulnerabilidade em suas máquinas virtuais|
|Máquina|1|As máquinas virtuais devem ser migradas para novos recursos do AzureRM|Use Azure Resource Manager para que suas máquinas virtuais forneçam aprimoramentos de segurança como: RBAC (controle de acesso mais forte), melhor auditoria, implantação e governança baseadas no Resource Manager, acesso a identidades gerenciadas, acesso ao key Vault para segredos, Autenticação baseada no Azure AD e suporte para marcas e grupos de recursos para facilitar o gerenciamento da segurança. |
|Máquina|30|Vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidade|As máquinas virtuais para as quais uma solução de avaliação de vulnerabilidades de terceiros são implantadas estão sendo continuamente avaliadas em relação a vulnerabilidades de aplicativo e sistema operacional. Sempre que essas vulnerabilidades forem encontradas, elas estarão disponíveis para obter mais informações como parte da recomendação.|
|Conjuntos de dimensionamento de máquinas virtuais |4|Os logs de diagnóstico em conjuntos de dimensionamento de máquinas virtuais devem ser habilitados|Habilite os logs e mantenha-os por até um ano. Isso permite que você recrie trilhas de atividade para fins de investigação. Isso é útil quando ocorre um incidente de segurança ou sua rede é comprometida.|
|Conjuntos de dimensionamento de máquinas virtuais|35|Vulnerabilidades na configuração de segurança em seus conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas|Corrija vulnerabilidades na configuração de segurança em seus conjuntos de dimensionamento de máquinas virtuais para protegê-las contra ataques. |
|Conjuntos de dimensionamento de máquinas virtuais|5|Corrigir falhas de integridade do Endpoint Protection em conjuntos de dimensionamento de máquinas virtuais|Corrija as falhas de integridade da proteção de ponto de extremidade em seus conjuntos de dimensionamento de máquinas virtuais para protegê-las contra ameaças e vulnerabilidades. |
|Conjuntos de dimensionamento de máquinas virtuais|10|O Endpoint Protection deve ser instalado em máquinas virtuais|Instale uma solução de proteção de ponto de extremidade em seus conjuntos de dimensionamento de máquinas virtuais para protegê-los contra ameaças e vulnerabilidades. |
|Conjuntos de dimensionamento de máquinas virtuais|40|As atualizações do sistema em conjuntos de dimensionamento de máquinas virtuais devem ser instaladas|Instale as atualizações críticas e de segurança do sistema ausentes para proteger seus conjuntos de dimensionamento de máquinas virtuais Windows e Linux. |
 





## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte:


* [Proteger as máquinas e aplicações no Centro de Segurança do Azure](security-center-virtual-machine-protection.md)
* [Monitor identity and access in Azure Security Center](security-center-identity-access.md) (Monitorizar a identidade e o acesso no Centro de Segurança do Azure)
* [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)
* [Protegendo seu serviço SQL do Azure na central de segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.

