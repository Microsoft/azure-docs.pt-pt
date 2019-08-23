---
title: Proteger as máquinas e aplicações no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento aborda recomendações no Centro de segurança que o ajudam a proteger as suas máquinas virtuais e computadores e suas aplicações web e ambientes de serviço de aplicações.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: v-mohabe
ms.openlocfilehash: cb6025b47aec50121a0f96f7b6cba7ad726db291
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907959"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Proteger as máquinas e aplicações no Centro de segurança do Azure
A central de segurança do Azure analisa o estado de segurança de seus recursos do Azure, servidores não Azure e máquinas virtuais. Quando o Centro de segurança identifica potenciais vulnerabilidades de segurança, cria recomendações que descreve o processo de configuração de controlos necessários. Recomendações se aplicam a tipos de recursos do Azure: máquinas virtuais (VMs) e computadores, aplicações, redes, SQL e identidade e acesso.

Este artigo aborda recomendações que se aplicam a máquinas e aplicações.

## <a name="monitoring-security-health"></a>Monitorizar o estado de funcionamento de segurança
Pode monitorizar o estado de segurança dos seus recursos na **Centro de segurança – descrição geral** dashboard. O **recursos** seção fornece o número de problemas identificados e o estado de segurança para cada tipo de recurso.

Pode ver uma lista de todos os problemas, selecionando **recomendações**. Para obter mais informações sobre como aplicar recomendações, veja [implementar recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md).

Para recomendações de serviços de uma lista completa de computação e de aplicação, consulte [recomendações](security-center-virtual-machine-protection.md#compute-and-app-recommendations).

Para continuar, selecione **computação e aplicações** sob **recursos** ou o menu principal do Centro de segurança.
![Dashboard do Centro de segurança](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>Monitorar computação e serviços de aplicativos
Em **computação & aplicativos**, há as seguintes guias:

- **Descrição geral**: monitorização e recomendações identificadas pelo Centro de Segurança.
- **VMs e computadores**: lista das suas VMS, computadores, máquinas virtuais e os atuais estados de segurança de cada um.
- **Serviços Cloud**: lista das suas funções de trabalho e na Web monitorizadas pelo Centro de Segurança.
- **Serviços de aplicativos**: lista de seus ambientes de serviço de aplicativo e o estado de segurança atual de cada um.
- **Contêineres (versão prévia)** : lista de seus contêineres hospedados em máquinas Linux IaaS e avaliação de segurança de suas configurações do Docker.
- **Recursos de computação (versão prévia)** : lista de recomendações para seus recursos de computação, como clusters de Service Fabric e hubs de eventos.

Para continuar, selecione **computação & aplicativos** em **higiene de segurança de recurso**.

![Computação](./media/security-center-virtual-machine-recommendations/compute.png)

Existem várias secções em cada separador e pode selecionar, em cada uma delas, uma opção individual para ver mais detalhes sobre os passos recomendados para resolver esse problema em concreto.

### Computadores e VMs não monitorizadas <a name="unmonitored-vms-and-computers"></a>
Uma VM ou o computador não é monitorizado pelo centro de segurança se a máquina não está em execução a extensão do Microsoft Monitoring Agent. Um computador pode ter um agente local já instalado, por exemplo, o agente direto do OMS ou o agente de System Center Operations Manager. As máquinas com estes agentes são identificadas como não monitorizado porque estes agentes não são totalmente suportados no Centro de segurança. Para beneficiar totalmente de todas as capacidades do Centro de Segurança, é necessária a extensão MMA.

Pode instalar a extensão no computador, além do agente local já instalado ou VM não monitorizada. Configure ambos os agentes da mesma forma, ligando-os à mesma área de trabalho. Isto permite ao Centro de Segurança interagir com a extensão MMA e recolher dados. Consulte [Ativar a extensão VM](../azure-monitor/learn/quick-collect-azurevm.md) para obter instruções sobre como instalar a extensão MMA.

Consulte [problemas de estado do agente de monitorização](security-center-troubleshooting-guide.md#mon-agent) para saber mais sobre os motivos pelos quais o Centro de Segurança não consegue monitorizar com êxito VMs e computadores inicializados para aprovisionamento automático.

### <a name="recommendations"></a>Recomendações
Esta secção tem um conjunto de recomendações para cada VM e o computador, a funções web e de trabalho, a aplicações de Web do serviço de aplicações do Azure e o ambiente de serviço de aplicações do Azure que monitoriza o Centro de segurança. A primeira coluna indica a recomendação. A segunda coluna mostra o número total de recursos que são afetados por essa recomendação. A terceira coluna mostra a gravidade do problema.

Cada recomendação tem um conjunto de ações que pode realizar depois de a selecionar. Por exemplo, se você selecionar **atualizações do sistema ausentes**, o número de VMs e computadores com patches ausentes e a severidade da atualização ausente será exibida.

**Aplicar atualizações do sistema** tem um resumo das atualizações críticas num formato de gráfico, um para Windows e outro para Linux. A segunda parte tem uma tabela com as seguintes informações:

- **NOME**: Nome da atualização ausente.
- **N.º DE VMs & computadores**: Número total de VMs e computadores sem esta atualização.
- **GRAVIDADE DA ATUALIZAÇÃO**: Descreve a severidade dessa recomendação específica:

    - **Crítico**: Existe uma vulnerabilidade com um recurso significativo (aplicativo, máquina virtual ou grupo de segurança de rede) e requer atenção.
    - **Importante**: Etapas não críticas ou adicionais são necessárias para concluir um processo ou eliminar uma vulnerabilidade.
    - **Moderado**: Uma vulnerabilidade deve ser resolvida, mas não requer atenção imediata. (Por predefinição, as recomendações baixas não são apresentadas, mas pode filtrar por recomendações baixas se pretender visualizá-las).


- **ESTADO**: O estado atual da recomendação:

    - **Abrir**: A recomendação ainda não foi resolvida.
    - **Em andamento**: A recomendação está sendo aplicada atualmente a esses recursos e nenhuma ação é exigida por você.
    - **Resolvido**: A recomendação já foi concluída. (Quando o problema for resolvido, a entrada fica a cinzento).

Para ver os detalhes das recomendações, clique no nome da atualização em falta na lista.


> [!NOTE]
> As recomendações de segurança aqui são as mesmas que as sob o **recomendações** mosaico. Ver [implementar recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) para obter mais informações sobre como resolver recomendações.
>
>

### <a name="vms-and-computers"></a>VMs e computadores
A secção de VMs e computadores dá-lhe uma descrição geral de todas as recomendações de VM e o computador. Cada coluna representa um conjunto de recomendações.

![Recomendações da VM e do computador](./media/security-center-virtual-machine-recommendations/vm-computers.png)

Existem quatro tipos de ícones representados nesta lista:

![Computador não pertencente ao Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Computador não Azure.

![VM do Azure Resource Manager](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) VM do Azure Resource Manager.

![VM clássica do Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) VM clássica do Azure.


![VMs identificadas a partir da área de trabalho](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) VMs que só são identificadas a partir da área de trabalho que faz parte da subscrição vista. Isso inclui VMs de outras assinaturas que são relatadas para o espaço de trabalho nessa assinatura e VMs que foram instaladas com o Operations Manager agente direto e não têm nenhuma ID de recurso.

O ícone apresentado em cada recomendação ajuda-o a identificar rapidamente a VM e o computador que precisa de atenção e o tipo de recomendação. Você também pode usar os filtros para pesquisar a lista por **tipo de recurso** e por **severidade**.

Para fazer uma busca detalhada nas recomendações de segurança para cada VM, clique na VM.
Aqui verá os detalhes de segurança para o computador ou VM. Na parte inferior, pode ver a ação recomendada e a gravidade de cada problema.
![Serviços cloud](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Serviços em nuvem
Para serviços de nuvem, uma recomendação é criada quando a versão do sistema operacional está desatualizada.

![Serviços em nuvem](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Num cenário em que tem uma recomendação (que não é o caso no exemplo anterior), tem de seguir os passos na recomendação para atualizar a versão do sistema operativo. Quando está disponível uma atualização, verá um alerta (vermelho ou cor de laranja, dependendo da gravidade do problema). Quando você seleciona esse alerta nas linhas WebRole1 (executa o Windows Server com seu aplicativo Web automaticamente implantado no IIS) ou WorkerRole1 (executa o Windows Server com seu aplicativo Web automaticamente implantado no IIS), você vê mais detalhes sobre essa recomendação.

Para ver uma explicação mais prescritiva sobre esta recomendação, clique em **Atualizar versão do SO** na coluna **DESCRIÇÃO**.



![Atualizar a versão do SO](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services"></a>Serviços aplicacionais
Você precisa habilitar o serviço de aplicativo em sua assinatura para exibir as informações do serviço de aplicativo. Para obter instruções sobre como habilitar esse recurso, consulte [proteger o serviço de aplicativo com a central de segurança do Azure](security-center-app-services.md).
[!NOTE]
> Monitorização do serviço de aplicações está em pré-visualização e disponível apenas no escalão Standard do Centro de segurança.


Sob **dos serviços de aplicações**, encontrar uma lista dos seus ambientes de serviço de aplicações e o estado de funcionamento resumo com base na avaliação do Centro de segurança efetuada.

![Serviços aplicacionais](./media/security-center-virtual-machine-recommendations/app-services.png)

Existem três tipos de ícones representados nesta lista:

![Ambiente de serviços de aplicações](./media/security-center-virtual-machine-recommendations/ase.png) Ambiente de serviços de aplicações.

![Aplicação Web](./media/security-center-virtual-machine-recommendations/web-app.png) Aplicação Web.

![Aplicação de função](./media/security-center-virtual-machine-recommendations/function-app.png) Aplicação de função.

1. Selecione uma aplicação web. É aberta uma vista de resumida com três separadores:

   - **Recomendações**: com base nas avaliações realizadas pelo centro de segurança que falharam.
   - **Passados avaliações**: lista de avaliações realizadas pelo centro de segurança passado.
   - **Avaliações indisponíveis**: lista de avaliações de que não foi possível executar devido a um erro ou a recomendação não é relevante para o serviço de aplicações específico

   Sob **recomendações** é uma lista das recomendações para a aplicação web selecionada e a gravidade de cada recomendação.

   ![Recomendações de serviços de aplicativos](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. Selecione uma recomendação para ver uma descrição da recomendação e uma lista de recursos não íntegros, recursos íntegros e recursos não verificados.

   - Na coluna **avaliações passadas** , há uma lista de avaliações aprovadas.  Gravidade dessas avaliações é sempre verde.

   - Selecione uma avaliação com êxito da lista para obter uma descrição da avaliação, uma lista de recursos de mau e bom estado de funcionamento e uma lista de recursos não verificados. Há uma guia para os recursos de mau estado de funcionamento, mas essa lista está sempre vazia, uma vez que a avaliação passado.

     ![Correção do serviço de aplicativo](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)

## <a name="virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquinas virtuais
A central de segurança descobre automaticamente se você tem conjuntos de escala e recomenda que você instale o Microsoft Monitoring Agent nesses conjuntos de dimensionamento. 

Para instalar o Microsoft Monitoring Agent: 

1. Selecione a recomendação **instalar o agente de monitoramento no conjunto de dimensionamento de máquinas virtuais**. Você Obtém uma lista de conjuntos de dimensionamento não monitorados.
2. Selecione um conjunto de dimensionamento não íntegro. Siga as instruções para instalar o agente de monitoramento usando um espaço de trabalho preenchido existente ou crie um novo. Certifique-se de definir o [tipo de preço](security-center-pricing.md) do espaço de trabalho se ele não estiver definido.

   ![Instalar o MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Se você quiser definir novos conjuntos de dimensionamento para instalar automaticamente o Microsoft Monitoring Agent:
1. Vá para Azure Policy e clique em **definições**.
2. Procure a política **implantar log Analytics agente para conjuntos de dimensionamento de máquinas virtuais do Windows** e clique nele.
3. Clique em **Atribuir**.
4. Defina o **escopo** e **log Analytics espaço de trabalho** e clique em **atribuir**.

Se você quiser definir todos os conjuntos de dimensionamento existentes para instalar o Microsoft Monitoring Agent, em Azure Policy, vá para **correção** e aplique a política existente aos conjuntos de dimensionamento existentes.


## <a name="compute-and-app-recommendations"></a>Recomendações de computação e de aplicação
|Tipo de recurso|Classificação de segurança|Recomendação|Descrição|
|----|----|----|----|
|Serviço de aplicações|20|Aplicação Web só deve estar acessível através de HTTPS|Limitar apenas o acesso de aplicações Web através de HTTPS.|
|Serviço de aplicações|20|Função de aplicação só deve estar acessível através de HTTPS|Limitar apenas o acesso de aplicações de funções através de HTTPS.|
|Serviço de aplicações|5|Os logs de diagnóstico nos serviços de aplicativos devem ser habilitados|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|Serviço de aplicações|10|Depuração remota deve ser desativada para a aplicação Web|Desative a depuração para aplicativos Web, se já não precisar de utilizá-lo. Depuração remota necessita de portas de entrada estar abertas na aplicação de funções.|
|Serviço de aplicações|10|Depuração remota deve ser desativada para a aplicação de função|Desative a depuração para a aplicação de função se já não precisar de utilizá-lo. Depuração remota necessita de portas de entrada estar abertas na aplicação de funções.|
|Serviço de aplicações|10|Não permitir que todos os ('* ') recursos para aceder à sua aplicação| Não permitir o conjunto de parâmetro WEBSITE_LOAD_CERTIFICATES "". Definição do parâmetro ' significa que todos os certificados são carregados para o arquivo de certificados pessoais de aplicações web. Isto pode levar ao abuso do princípio de privilégio mínimo porque é improvável que o site necessita de acesso a todos os certificados no tempo de execução.|
|Serviço de aplicações|20|CORS não deve permitir que todos os recursos aceder às suas aplicações Web|Permitir que apenas necessários domínios interagir com a sua aplicação web. Entre os recursos de origens (CORS) de partilha não deve permitir que todos os domínios acedam à sua aplicação web.|
|Serviço de aplicações|20|CORS não deve permitir que todos os recursos de aceder à sua aplicação de função| Permitir que apenas necessários domínios interagir com a sua aplicação de função. Entre os recursos de origens (CORS) de partilha não deve permitir que todos os domínios acedam à sua aplicação de função.|
|(Batch) de recursos de computação|1|As regras de alerta de métrica devem ser configuradas em contas do lote|Configurar regras de alerta de métrica na conta do Batch e ativar as métricas de conjunto eliminar eventos completa e eventos de eliminar conjunto|
|(Recursos de infraestrutura do serviço) de recursos de computação|10|Clusters de Service Fabric só devem usar Azure Active Directory para autenticação de cliente|Efetue a autenticação de cliente apenas através do Azure Active Directory no Service Fabric.|
|(Conta de automatização) de recursos de computação|5|As variáveis da conta de automação devem ser criptografadas|Ative a encriptação de recursos de variável de conta de automatização ao armazenar dados confidenciais.|
|(Pesquisa) de recursos de computação|5|Habilitação de auditoria de logs de diagnóstico para serviços de pesquisa|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|(Barramento de serviço) de recursos de computação|5|Os logs de diagnóstico no barramento de serviço devem ser habilitados|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|(O stream analytics) de recursos de computação|5|Os logs de diagnóstico no Azure Stream Analytics devem ser habilitados|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|(Batch) de recursos de computação|5|Ativar registos de diagnóstico em contas do Batch|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|(Hub de eventos) de recursos de computação|5|Os logs de diagnóstico no Hub de eventos devem ser habilitados|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|(Aplicações lógicas) de recursos de computação|5|Ativar registos de diagnóstico no Logic Apps|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|(Recursos de infraestrutura do serviço) de recursos de computação|15|Defina a propriedade ClusterProtectionLevel EncryptAndSign no Service Fabric|O Service Fabric fornece três níveis de proteção (nenhum, início de sessão e EncryptAndSign) para a comunicação de nó para nó a utilizar um certificado de cluster principal.  Defina o nível de proteção para garantir que todas as mensagens de nó para nó são criptografadas e assinadas digitalmente. |
|(Barramento de serviço) de recursos de computação|1|Remover todas as regras de autorização, exceto RootManageSharedAccessKey do espaço de nomes do Service Bus |Os clientes do Service Bus não devem utilizar uma política de acesso de nível de espaço de nomes que fornece acesso a todas as filas e tópicos num espaço de nomes. Para alinhar com o modelo de segurança de privilégios mínimos, você deve criar políticas de acesso no nível de entidade para filas e tópicos para fornecer acesso apenas à entidade específica.|
|(Hub de eventos) de recursos de computação|1|Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do namespace do hub de eventos|Os clientes do Hub de eventos não devem utilizar uma política de acesso de nível de espaço de nomes que fornece acesso a todas as filas e tópicos num espaço de nomes. Para alinhar com o modelo de segurança de privilégios mínimos, você deve criar políticas de acesso no nível de entidade para filas e tópicos para fornecer acesso apenas à entidade específica.|
|(Hub de eventos) de recursos de computação|5|As regras de autorização na entidade do hub de eventos devem ser definidas|Regras de autorização na entidade para conceder acesso menos privilegiado Hub de eventos de auditoria.|
|Machine|50|Instalar o agente de monitorização nos seus computadores|Instale o agente de monitorização para ativar a recolha de dados, as atualizações de verificação, verificação de linha de base e o endpoint protection em cada máquina.|
|Máquina|50|Ativar o aprovisionamento automático e a recolha de dados para as suas subscrições |Ative aprovisionamento automático e a recolha de dados para máquinas nas suas subscrições para ativar a recolha de dados, as atualizações de verificação, verificação de linha de base e o endpoint protection em cada máquina adicionada às suas subscrições.|
|Máquina|40|Resolver problemas de estado de funcionamento de agente de monitorização nos seus computadores|Para proteção de centro de segurança completa, resolver problemas de agente de monitorização em suas máquinas, seguindo as instruções no guia de resolução de problemas| 
|Máquina|40|Resolver problemas de estado de funcionamento do Endpoint Protection nos seus computadores|Para proteção de centro de segurança completa, resolva problemas de agente de monitorização em suas máquinas, seguindo as instruções no guia de resolução de problemas.|
|Máquina|40|Resolver problemas relacionados com dados de análise em falta os seus computadores|Resolver problemas relacionados com dados de análise em falta nas máquinas virtuais e computadores. Dados de análise em falta nos seus resultados de máquinas na ausência de avaliações de segurança, tais como atualizar a análise de linha de base de análise e falta de verificação da solução de proteção de ponto final.|
|Máquina|40|As atualizações do sistema devem ser instaladas em seus computadores|Instalar a segurança do sistema em falta e atualizações críticas para proteger as máquinas virtuais Windows e Linux e computadores
|Machine|15|Adicionar uma firewall de aplicação Web| Implemente uma solução de firewall (WAF) de aplicações web para proteger seus aplicativos web. |
|Machine|40|Atualizar a versão do SO para as funções de serviço na cloud|Atualize a versão do sistema operativo (SO) para as funções de serviço em cloud para a versão mais recente disponível para a sua família de SO.|
|Máquina|35|Vulnerabilidades na configuração de segurança em seus computadores devem ser corrigidas|Remediar vulnerabilidades na configuração de segurança nos seus computadores para protegê-los contra ataques.|
|Máquina|35|Remediar vulnerabilidades na configuração de segurança em seus contentores|Remediar vulnerabilidades na configuração de segurança em máquinas com o Docker para protegê-las de ataques.|
|Máquina|25|Ativar Controlos de Aplicação Adaptáveis|Ative a aplicação controle para controle que aplicações podem ser executadas nas suas VMs localizadas no Azure. Isto ajudará a proteger as VMs contra software maligno. Centro de segurança utiliza machine learning para analisar as aplicações em execução em cada VM e ajuda a que aplicar permite regras com base neste informações. Esta capacidade simplifica o processo de configuração e manutenção de aplicação de regras de permissão.|
|Máquina|20|Instalar a solução do Endpoint Protection nos seus computadores|Instale uma solução de proteção de ponto final em suas máquinas virtuais, para protegê-los de ameaças e vulnerabilidades.|
|Máquina|20|Reiniciar os seus computadores para aplicar as atualizações do sistema|Reinicie os seus computadores para aplicar as atualizações de sistema e proteger o computador contra vulnerabilidades.|
|Machine|15|A criptografia de disco deve ser aplicada em máquinas virtuais|Encripte discos da máquina virtual com o Azure Disk Encryption ambos os para máquinas virtuais Windows e Linux. Encriptação de disco do Azure (ADE) aproveita a funcionalidade de BitLocker padrão da indústria do Windows e a funcionalidade de DM-Crypt do Linux para fornecer encriptação de disco de SO e dados para ajudar a proteger e salvaguardar os seus dados e ajudam a cumprir a conformidade e segurança organizacionais compromissos no cliente do Azure key vault. Quando seu requisito de conformidade e segurança exige que encripte os dados de ponta a ponta com as suas chaves de encriptação, incluindo a encriptação do disco (temporária ligado localmente) efémeras, utilize a encriptação de discos do Azure. Em alternativa, por predefinição, o Managed Disks são encriptados em descanso por padrão, usando a encriptação do serviço de armazenamento do Azure onde as chaves de encriptação são chaves gerida pela Microsoft no Azure. Se esta implementação satisfaz os requisitos de segurança e conformidade, pode aproveitar a encriptação de disco gerido padrão para satisfazer os seus requisitos.|
|Machine|30|Instale uma solução de avaliação de vulnerabilidades nas suas máquinas virtuais|Instale uma solução de avaliação de vulnerabilidades nas suas máquinas virtuais|
|Máquina|15|Adicionar uma firewall de aplicação Web| Implemente uma solução de firewall (WAF) de aplicações web para proteger seus aplicativos web. |
|Máquina|30|Vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidade|As máquinas virtuais para o qual é implementar uma solução de terceiros de 3 de avaliação do vulnerabilidade estão a ser continuamente avaliadas em relação a aplicação e vulnerabilidades do SO. Sempre que encontram-se estas vulnerabilidades, elas estão disponíveis para obter mais informações, como parte da recomendação.|
|Machine|30|Instale uma solução de avaliação de vulnerabilidades nas suas máquinas virtuais|Instale uma solução de avaliação de vulnerabilidades nas suas máquinas virtuais|
|Machine|1|As máquinas virtuais devem ser migradas para novos recursos do AzureRM|Use Azure Resource Manager para que suas máquinas virtuais forneçam aprimoramentos de segurança como: RBAC (controle de acesso mais forte), melhor auditoria, implantação e governança baseadas no Resource Manager, acesso a identidades gerenciadas, acesso ao key Vault para segredos, Autenticação baseada no Azure AD e suporte para marcas e grupos de recursos para facilitar o gerenciamento da segurança. |
|Machine|30|Vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidade|As máquinas virtuais para o qual é implementar uma solução de terceiros de 3 de avaliação do vulnerabilidade estão a ser continuamente avaliadas em relação a aplicação e vulnerabilidades do SO. Sempre que encontram-se estas vulnerabilidades, elas estão disponíveis para obter mais informações, como parte da recomendação.|
|Conjunto de dimensões da máquina virtual |4|Os logs de diagnóstico em conjuntos de dimensionamento de máquinas virtuais devem ser habilitados|Habilite os logs e mantenha-os por até um ano. Isso permite que você recrie trilhas de atividade para fins de investigação. Isso é útil quando ocorre um incidente de segurança ou sua rede é comprometida.|
|Conjunto de dimensões da máquina virtual|35|Vulnerabilidades na configuração de segurança em seus conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas|Corrija vulnerabilidades na configuração de segurança em seus conjuntos de dimensionamento de máquinas virtuais para protegê-las contra ataques. |
|Conjunto de dimensões da máquina virtual|5|Corrigir falhas de integridade do Endpoint Protection em conjuntos de dimensionamento de máquinas virtuais|Corrija as falhas de integridade da proteção de ponto de extremidade em seus conjuntos de dimensionamento de máquinas virtuais para protegê-las contra ameaças e vulnerabilidades. |
|Conjunto de dimensões da máquina virtual|10|O Endpoint Protection deve ser instalado em máquinas virtuais|Instale uma solução de proteção de ponto de extremidade em seus conjuntos de dimensionamento de máquinas virtuais para protegê-los contra ameaças e vulnerabilidades. |
|Conjunto de dimensões da máquina virtual|40|As atualizações do sistema em conjuntos de dimensionamento de máquinas virtuais devem ser instaladas|Instale as atualizações críticas e de segurança do sistema ausentes para proteger seus conjuntos de dimensionamento de máquinas virtuais Windows e Linux. |
 





## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte:


* [Proteger as máquinas e aplicações no Centro de Segurança do Azure](security-center-virtual-machine-protection.md)
* [Monitorizar a identidade e acesso no Centro de segurança do Azure](security-center-identity-access.md)
* [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)
* [Proteger o seu serviço do SQL do Azure no Centro de segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.

