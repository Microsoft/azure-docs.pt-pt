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
ms.date: 2/25/2019
ms.author: monhaber
ms.openlocfilehash: 73a88ec0280728b4a485bd095096e90bbc43ef40
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990956"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Proteger as máquinas e aplicações no Centro de segurança do Azure
Centro de segurança do Azure analisa o estado de segurança dos seus recursos do Azure. Quando o Centro de segurança identifica potenciais vulnerabilidades de segurança, cria recomendações que descreve o processo de configuração de controlos necessários. Recomendações se aplicam a tipos de recursos do Azure: máquinas virtuais (VMs) e computadores, aplicações, redes, SQL e identidade e acesso.

Este artigo aborda recomendações que se aplicam a máquinas e aplicações.

## <a name="monitoring-security-health"></a>Monitorizar o estado de funcionamento de segurança
Pode monitorizar o estado de segurança dos seus recursos na **Centro de segurança – descrição geral** dashboard. O **recursos** seção fornece o número de problemas identificados e o estado de segurança para cada tipo de recurso.

Pode ver uma lista de todos os problemas, selecionando **recomendações**. Para obter mais informações sobre como aplicar recomendações, veja [implementar recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md).

Para recomendações de serviços de uma lista completa de computação e de aplicação, consulte [recomendações](security-center-virtual-machine-recommendations.md).

Para continuar, selecione **computação e aplicações** sob **recursos** ou o menu principal do Centro de segurança.
![Dashboard do Centro de segurança](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>Monitorizar os serviços de computação e de aplicação
Sob **computação e aplicações**, existem os seguintes separadores:

- **Descrição geral**: monitorização e recomendações identificadas pelo Centro de Segurança.
- **VMs e computadores**: lista das suas VMS, computadores, máquinas virtuais e os atuais estados de segurança de cada um.
- **Serviços Cloud**: lista das suas funções de trabalho e na Web monitorizadas pelo Centro de Segurança.
- **Serviços aplicacionais**: lista de seus ambientes do serviço de aplicações e o estado de segurança atual de cada.
- **Contentores (pré-visualização)**: lista de contentores do alojadas em máquinas do IaaS Linux e a avaliação de segurança de suas configurações de Docker.
- **(Pré-visualização) de recursos de computação**: lista de recomendações para os seus recursos de computação, tais como clusters do Service Fabric e dos hubs de eventos.

Para continuar, selecione **computação e aplicações** sob **higiene de troca de segurança de recursos**.

![Computação](./media/security-center-virtual-machine-recommendations/compute.png)

Existem várias secções em cada separador e pode selecionar, em cada uma delas, uma opção individual para ver mais detalhes sobre os passos recomendados para resolver esse problema em concreto.

### Computadores e VMs não monitorizadas <a name="unmonitored-vms-and-computers"></a>
Uma VM ou o computador não é monitorizado pelo centro de segurança se a máquina não está em execução a extensão do Microsoft Monitoring Agent. Um computador pode ter um agente local já instalado, por exemplo o OMS direcionar o agente ou o agente do SCOM. As máquinas com estes agentes são identificadas como não monitorizado porque estes agentes não são totalmente suportados no Centro de segurança. Para beneficiar totalmente de todas as capacidades do Centro de Segurança, é necessária a extensão MMA.

Pode instalar a extensão no computador, além do agente local já instalado ou VM não monitorizada. Configure ambos os agentes da mesma forma, ligando-os à mesma área de trabalho. Isto permite ao Centro de Segurança interagir com a extensão MMA e recolher dados. Consulte [Ativar a extensão VM](../azure-monitor/learn/quick-collect-azurevm.md) para obter instruções sobre como instalar a extensão MMA.

Consulte [problemas de estado do agente de monitorização](security-center-troubleshooting-guide.md#mon-agent) para saber mais sobre os motivos pelos quais o Centro de Segurança não consegue monitorizar com êxito VMs e computadores inicializados para aprovisionamento automático.

### <a name="recommendations"></a>Recomendações
Esta secção tem um conjunto de recomendações para cada VM e o computador, a funções web e de trabalho, a aplicações de Web do serviço de aplicações do Azure e o ambiente de serviço de aplicações do Azure que monitoriza o Centro de segurança. A primeira coluna indica a recomendação. A segunda coluna mostra o número total de recursos que são afetados por essa recomendação. A terceira coluna mostra a gravidade do problema.

Cada recomendação tem um conjunto de ações que pode realizar depois de a selecionar. Por exemplo, se selecionar **atualizações de sistema em falta**, é apresentado o número de VMs e computadores que têm patches em falta e a gravidade da atualização em falta.

**Aplicar atualizações do sistema** tem um resumo das atualizações críticas num formato de gráfico, um para Windows e outro para Linux. A segunda parte tem uma tabela com as seguintes informações:

- **NOME**: Nome da atualização em falta.
- **N.º DE VMs e computadores**: Número total de VMs e computadores que estão em falta esta atualização.
- **GRAVIDADE DA ATUALIZAÇÃO**: Descreve a gravidade dessa recomendação específica:

    - **Crítico**: Uma vulnerabilidade existe com um recurso significativo (aplicação, máquina virtual ou grupo de segurança de rede) e necessita de atenção.
    - **Importante**: Passos não críticos ou adicionais são necessários para concluir um processo ou eliminar uma vulnerabilidade.
    - **Moderado**: Uma vulnerabilidade deve ser tratada, mas não necessita de atenção imediata. (Por predefinição, as recomendações baixas não são apresentadas, mas pode filtrar por recomendações baixas se pretender visualizá-las).


- **ESTADO**: O estado atual da Recomendação:

    - **Abra**: A recomendação ainda não foi tratada.
    - **Em curso**: A recomendação está atualmente a ser aplicada a esses recursos, e é necessária nenhuma ação por si.
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


![VMs identificadas a partir da área de trabalho](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) VMs que só são identificadas a partir da área de trabalho que faz parte da subscrição vista. Inclui VMs de outras subscrições que reportam à área de trabalho desta subscrição e VMs que foram instaladas com o agente direto do SCOM e não têm ID de recurso.

O ícone apresentado em cada recomendação ajuda-o a identificar rapidamente a VM e o computador que precisa de atenção e o tipo de recomendação. Também pode utilizar os filtros para procurar a lista por **tipo de recurso** e por **gravidade**.

Para desagregar as recomendações de segurança para cada VM, clique na VM.
Aqui verá os detalhes de segurança para o computador ou VM. Na parte inferior, pode ver a ação recomendada e a gravidade de cada problema.
![Serviços cloud](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Serviços em nuvem
Serviços cloud, é criada uma recomendação quando a versão do sistema operativo está desatualizada.

![Serviços em nuvem](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Num cenário em que tem uma recomendação (que não é o caso no exemplo anterior), tem de seguir os passos na recomendação para atualizar a versão do sistema operativo. Quando está disponível uma atualização, verá um alerta (vermelho ou cor de laranja, dependendo da gravidade do problema). Quando seleciona este alerta no WebRole1 (executa o Windows Server com a sua aplicação web automaticamente implementada no IIS) ou WorkerRole1 (executa o Windows Server com a sua aplicação web automaticamente implementada no IIS) linhas, pode ver mais detalhes sobre esta recomendação.

Para ver uma explicação mais prescritiva sobre esta recomendação, clique em **Atualizar versão do SO** na coluna **DESCRIÇÃO**.



![Atualizar a versão do SO](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services"></a>Serviços aplicacionais
Tem de ativar o serviço de aplicações na sua subscrição para ver as informações do serviço de aplicações. Para obter instruções sobre como ativar esta funcionalidade, consulte [proteger o serviço de aplicações com o Centro de segurança do Azure](security-center-app-services.md).
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

  ![Recomendações de serviços de aplicação](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. Selecione uma recomendação para ver uma descrição da recomendação e obter uma lista de recursos de mau estado de funcionamento, recursos de bom estado de funcionamento e recursos não verificados.

 - Sob o **transmitido avaliações** coluna é uma lista de avaliações aprovadas.  Gravidade dessas avaliações é sempre verde.

 -  Selecione uma avaliação com êxito da lista para obter uma descrição da avaliação, uma lista de recursos de mau e bom estado de funcionamento e uma lista de recursos não verificados. Há uma guia para os recursos de mau estado de funcionamento, mas essa lista está sempre vazia, uma vez que a avaliação passado.

    ![Atualização do serviço de aplicações](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)

## <a name="virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquinas virtuais
Centro de segurança Deteta automaticamente se têm as conjuntos de escalas e recomenda que instale o Microsoft Monitoring Agent nestes conjuntos de dimensionamento. 

Para instalar o Microsoft Monitoring Agent: 

1. Selecione a recomendação **instalar o agente de monitorização no conjunto de dimensionamento de máquina virtual**. Obter uma lista de conjuntos de dimensionamento não monitorizado.
2. Selecione um conjunto de dimensionamento de mau estado de funcionamento. Siga as instruções para instalar o agente de monitorização através de uma área de trabalho preenchida existente ou crie um novo. Certifique-se de definir a área de trabalho [escalão de preço](security-center-pricing.md) se não estiver definido.

 ![Instalar MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Se pretender definir conjuntos de dimensionamento de novo para instalar automaticamente o Microsoft Monitoring Agent:
1. Aceda à política do Azure e clique em **definições**.
2. Pesquisa para a política **agente de implementar o Log Analytics para conjuntos de dimensionamento de VM do Windows** e clique no mesmo.
3. Clique em **Atribuir**.
4. Definir o **âmbito** e **área de trabalho do Log Analytics** e clique em **atribuir**.

Se pretender definir existente todos os conjuntos de dimensionamento para instalar o Microsoft Monitoring Agent, no Azure Policy, aceda a **remediação** e aplicar a política existente para conjuntos de dimensionamento existente.


## <a name="compute-and-app-recommendations"></a>Recomendações de computação e de aplicação
|Tipo de recurso|Classificação de segurança|Recomendação|Descrição|
|----|----|----|----|
|Serviço de aplicações|20|Aplicação Web só deve estar acessível através de HTTPS|Limitar apenas o acesso de aplicações Web através de HTTPS.|
|Serviço de aplicações|20|Função de aplicação só deve estar acessível através de HTTPS|Limitar apenas o acesso de aplicações de funções através de HTTPS.|
|Serviço de aplicações|5|Ativar registos de diagnóstico no serviço de aplicações|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|Serviço de aplicações|10|Depuração remota deve ser desativada para a aplicação Web|Desative a depuração para aplicativos Web, se já não precisar de utilizá-lo. Depuração remota necessita de portas de entrada estar abertas na aplicação de funções.|
|Serviço de aplicações|10|Depuração remota deve ser desativada para a aplicação de função|Desative a depuração para a aplicação de função se já não precisar de utilizá-lo. Depuração remota necessita de portas de entrada estar abertas na aplicação de funções.|
|Serviço de aplicações|10|Configurar restrições de IP para a aplicação Web|Defina uma lista de endereços IP que têm permissão para aceder à sua aplicação. Utilização de restrições de IP de protege as aplicações web contra ataques comuns.|
|Serviço de aplicações|10|Não permitir que todos os ('* ') recursos para aceder à sua aplicação| Não permitir o conjunto de parâmetro WEBSITE_LOAD_CERTIFICATES "". Definição do parâmetro ' significa que todos os certificados são carregados para o arquivo de certificados pessoais de aplicações web. Isto pode levar ao abuso do princípio de privilégio mínimo porque é improvável que o site necessita de acesso a todos os certificados no tempo de execução.|
|Serviço de aplicações|20|CORS não deve permitir que todos os recursos aceder às suas aplicações Web|Permitir que apenas necessários domínios interagir com a sua aplicação web. Entre os recursos de origens (CORS) de partilha não deve permitir que todos os domínios acedam à sua aplicação web.|
|Serviço de aplicações|20|CORS não deve permitir que todos os recursos de aceder à sua aplicação de função| Permitir que apenas necessários domínios interagir com a sua aplicação de função. Entre os recursos de origens (CORS) de partilha não deve permitir que todos os domínios acedam à sua aplicação de função.|
|(Batch) de recursos de computação|1|Configurar regras de alerta de métrica na conta de Batch|Configurar regras de alerta de métrica na conta do Batch e ativar as métricas de conjunto eliminar eventos completa e eventos de eliminar conjunto|
|(Recursos de infraestrutura do serviço) de recursos de computação|10|Utilizar o Azure Active Directory para autenticação de cliente no Service Fabric|Efetue a autenticação de cliente apenas através do Azure Active Directory no Service Fabric.|
|(Conta de automatização) de recursos de computação|5| Ativar a encriptação da conta de automatização|Ative a encriptação de recursos de variável de conta de automatização ao armazenar dados confidenciais.|
|(Balanceador de carga) de recursos de computação|5|Ativar registos de diagnóstico no balanceador de carga|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|(Pesquisa) de recursos de computação|5|Ativar registos de diagnóstico no serviço de pesquisa|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|(Barramento de serviço) de recursos de computação|5|Ativar registos de diagnóstico no Service Bus|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|(O stream analytics) de recursos de computação|5|Ativar registos de diagnóstico no Azure Stream Analytics|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|(Recursos de infraestrutura do serviço) de recursos de computação|5|Ativar registos de diagnóstico no Service Fabric|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|(Batch) de recursos de computação|5|Ativar registos de diagnóstico em contas do Batch|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|(Hub de eventos) de recursos de computação|5|Ativar registos de diagnóstico no Hub de eventos|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|(Aplicações lógicas) de recursos de computação|5|Ativar registos de diagnóstico no Logic Apps|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|(Recursos de infraestrutura do serviço) de recursos de computação|15|Defina a propriedade ClusterProtectionLevel EncryptAndSign no Service Fabric|O Service Fabric fornece três níveis de proteção (nenhum, início de sessão e EncryptAndSign) para a comunicação de nó para nó a utilizar um certificado de cluster principal.  Defina o nível de proteção para garantir que todas as mensagens de nó para nó são criptografadas e assinadas digitalmente. |
|(Barramento de serviço) de recursos de computação|1|Remover todas as regras de autorização, exceto RootManageSharedAccessKey do espaço de nomes do Service Bus |Os clientes do Service Bus não devem utilizar uma política de acesso de nível de espaço de nomes que fornece acesso a todas as filas e tópicos num espaço de nomes. Para alinhar com o menor privilégio modelo de segurança, deve criar políticas de acesso no nível de entidade para filas e tópicos para fornecer acesso a apenas a entidade específica.|
|(Hub de eventos) de recursos de computação|1|Remover todas as regras de autorização, exceto RootManageSharedAccessKey do espaço de nomes do Hub de eventos |Os clientes do Hub de eventos não devem utilizar uma política de acesso de nível de espaço de nomes que fornece acesso a todas as filas e tópicos num espaço de nomes. Para alinhar com o menor privilégio modelo de segurança, deve criar políticas de acesso no nível de entidade para filas e tópicos para fornecer acesso a apenas a entidade específica.|
|(Hub de eventos) de recursos de computação|5|Definir regras de autorização na entidade Hub de eventos|Regras de autorização na entidade para conceder acesso menos privilegiado Hub de eventos de auditoria.|
|Máquina|50|Instalar o agente de monitorização nos seus computadores|Instale o agente de monitorização para ativar a recolha de dados, as atualizações de verificação, verificação de linha de base e o endpoint protection em cada máquina.|
|Máquina|50|Ativar o aprovisionamento automático e a recolha de dados para as suas subscrições |Ative aprovisionamento automático e a recolha de dados para máquinas nas suas subscrições para ativar a recolha de dados, as atualizações de verificação, verificação de linha de base e o endpoint protection em cada máquina adicionada às suas subscrições.|
|Máquina|40|Resolver problemas de estado de funcionamento de agente de monitorização nos seus computadores|Para proteção de centro de segurança completa, resolver problemas de agente de monitorização em suas máquinas, seguindo as instruções no guia de resolução de problemas| 
|Máquina|40|Resolver problemas de estado de funcionamento do Endpoint Protection nos seus computadores|Para proteção de centro de segurança completa, resolva problemas de agente de monitorização em suas máquinas, seguindo as instruções no guia de resolução de problemas.|
|Máquina|40|Resolver problemas relacionados com dados de análise em falta os seus computadores|Resolver problemas relacionados com dados de análise em falta nas máquinas virtuais e computadores. Dados de análise em falta nos seus resultados de máquinas na ausência de avaliações de segurança, tais como atualizar a análise de linha de base de análise e falta de verificação da solução de proteção de ponto final.|
|Máquina|40|Instalar atualizações de sistema nos seus computadores|Instalar a segurança do sistema em falta e atualizações críticas para proteger as máquinas virtuais Windows e Linux e computadores
|Máquina|15|Adicionar uma firewall de aplicação Web| Implemente uma solução de firewall (WAF) de aplicações web para proteger seus aplicativos web. |
|Máquina|40|Atualizar a versão do SO para as funções de serviço na cloud|Atualize a versão do sistema operativo (SO) para as funções de serviço em cloud para a versão mais recente disponível para a sua família de SO.|
|Máquina|35|Solucionar vulnerabilidades na configuração de segurança nos seus computadores|Remediar vulnerabilidades na configuração de segurança nos seus computadores para protegê-los contra ataques. |
|Máquina|35|Remediar vulnerabilidades na configuração de segurança em seus contentores|Remediar vulnerabilidades na configuração de segurança em máquinas com o Docker para protegê-las de ataques.|
|Máquina|25|Ativar Controlos de Aplicação Adaptáveis|Ative a aplicação controle para controle que aplicações podem ser executadas nas suas VMs localizadas no Azure. Isto ajudará a proteger as VMs contra software maligno. Centro de segurança utiliza machine learning para analisar as aplicações em execução em cada VM e ajuda a que aplicar permite regras com base neste informações. Esta capacidade simplifica o processo de configuração e manutenção de aplicação de regras de permissão.|
|Máquina|20|Instalar a solução do Endpoint Protection nos seus computadores|Instale uma solução de proteção de ponto final em suas máquinas virtuais, para protegê-los de ameaças e vulnerabilidades.|
|Máquina|20|Reiniciar os seus computadores para aplicar as atualizações do sistema|Reinicie os seus computadores para aplicar as atualizações de sistema e proteger o computador contra vulnerabilidades.|
|Máquina|15|Aplicar encriptação de disco nas suas máquinas virtuais|Encripte discos da máquina virtual com o Azure Disk Encryption ambos os para máquinas virtuais Windows e Linux. Encriptação de disco do Azure (ADE) aproveita a funcionalidade de BitLocker padrão da indústria do Windows e a funcionalidade de DM-Crypt do Linux para fornecer encriptação de disco de SO e dados para ajudar a proteger e salvaguardar os seus dados e ajudam a cumprir a conformidade e segurança organizacionais compromissos no cliente do Azure key vault. Quando seu requisito de conformidade e segurança exige que encripte os dados de ponta a ponta com as suas chaves de encriptação, incluindo a encriptação do disco (temporária ligado localmente) efémeras, utilize a encriptação de discos do Azure. Em alternativa, por predefinição, o Managed Disks são encriptados em descanso por padrão, usando a encriptação do serviço de armazenamento do Azure onde as chaves de encriptação são chaves gerida pela Microsoft no Azure. Se esta implementação satisfaz os requisitos de segurança e conformidade, pode aproveitar a encriptação de disco gerido padrão para satisfazer os seus requisitos.|
|Máquina|30|Instale uma solução de avaliação de vulnerabilidades nas suas máquinas virtuais|Instale uma solução de avaliação de vulnerabilidades nas suas máquinas virtuais|
|Máquina|15|Adicionar uma firewall de aplicação Web| Implemente uma solução de firewall (WAF) de aplicações web para proteger seus aplicativos web. |
|Máquina|30|Remediar vulnerabilidades através de uma solução de avaliação de vulnerabilidade|As máquinas virtuais para o qual é implementar uma solução de terceiros de 3 de avaliação do vulnerabilidade estão a ser continuamente avaliadas em relação a aplicação e vulnerabilidades do SO. Sempre que encontram-se estas vulnerabilidades, elas estão disponíveis para obter mais informações, como parte da recomendação.|
|Máquina|30|Instale uma solução de avaliação de vulnerabilidades nas suas máquinas virtuais|Instale uma solução de avaliação de vulnerabilidades nas suas máquinas virtuais|
|Máquina|1|Migrar máquinas virtuais para novos recursos do Azure Resource Manager|Utilizar o Azure Resource Manager para as suas máquinas virtuais para fornecer aprimoramentos de segurança, tais como: aceder a mais forte controlo de acesso (RBAC), a auditoria melhor, a implantação baseada no Resource Manager e a governação de identidades geridas, acesso ao Cofre de chaves para segredos, Autenticação baseada no AD do Azure e o suporte para etiquetas e grupos de recursos para facilitar a gestão segurança. |
|Máquina|30|Remediar vulnerabilidades através de uma solução de avaliação de vulnerabilidade|As máquinas virtuais para o qual é implementar uma solução de terceiros de 3 de avaliação do vulnerabilidade estão a ser continuamente avaliadas em relação a aplicação e vulnerabilidades do SO. Sempre que encontram-se estas vulnerabilidades, elas estão disponíveis para obter mais informações, como parte da recomendação.|
|Conjuntos de dimensionamento de máquinas virtuais |4|Ativar os registos de diagnóstico nos Conjuntos de Dimensionamento de Máquinas Virtuais|Ativar os registos e mantenha-os para até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação. Isto é útil quando ocorrer um incidente de segurança ou a rede estiver comprometida.|
|Conjuntos de dimensionamento de máquinas virtuais|35|Remediar vulnerabilidades na configuração de segurança nos seus conjuntos de dimensionamento de máquinas virtuais|Remediar vulnerabilidades na configuração de segurança nos conjuntos de dimensionamento da máquina virtual para protegê-los de ataques. |
|Conjuntos de dimensionamento de máquinas virtuais|5|Remediar falhas do estado de funcionamento do Endpoint Protection em conjuntos de dimensionamento de máquinas virtuais|Corrija as falhas de estado de funcionamento de proteção do ponto final nos conjuntos de dimensionamento da máquina virtual para protegê-los de ameaças e vulnerabilidades. |
|Conjuntos de dimensionamento de máquinas virtuais|10|Instalar a solução do Endpoint Protection em conjuntos de dimensionamento de máquinas virtuais|Instale uma solução de proteção de ponto final em seus conjuntos de dimensionamento de máquina virtual, para protegê-los de ameaças e vulnerabilidades. |
|Conjuntos de dimensionamento de máquinas virtuais|40|Instalar atualizações do sistema em conjuntos de dimensionamento de máquinas virtuais|Instale o sistema de segurança em falta e as atualizações críticas para proteger os conjuntos de dimensionamento da máquina virtual do Windows e do Linux. |
 





## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte:


* [Recomendações do Centro de segurança do Azure de compreensão para máquinas virtuais](security-center-virtual-machine-recommendations.md)
* [Monitorizar a identidade e acesso no Centro de segurança do Azure](security-center-identity-access.md)
* [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)
* [Proteger o seu serviço do SQL do Azure no Centro de segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.

