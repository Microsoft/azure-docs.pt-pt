---
title: Visão geral da amostra de amostra de projeto HIPAA HITRUST 9.2
description: Visão geral da amostra de planta HIPAA HITRUST 9.2. Esta amostra de planta ajuda os clientes a avaliar controlos específicos do HIPAA HITRUST 9.2.
ms.date: 01/27/2021
ms.topic: sample
ms.openlocfilehash: bd65b6113f291457096bacc02bdbcfd92d6e0f84
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915551"
---
# <a name="hipaa-hitrust-92-blueprint-sample"></a>Amostra de planta HIPAA HITRUST 9.2

A amostra de planta HIPAA HITRUST 9.2 fornece guarda-trilhos de governação utilizando [a política Azure](../../policy/overview.md) que o ajuda a avaliar controlos específicos do HIPAA HITRUST 9.2. Este projeto ajuda os clientes a implementar um conjunto central de políticas para qualquer arquitetura implantada pelo Azure que deve implementar os controlos HIPAA HITRUST 9.2.

## <a name="control-mapping"></a>Mapeamento de controlo

O [mapeamento de controlo de política Azure](../../policy/samples/hipaa-hitrust-9-2.md) fornece detalhes sobre as definições de política incluídas neste projeto e como estas definições de política mapeiam para os **domínios** de conformidade e **controlos** no HIPAA HITRUST 9.2. Quando atribuídos a uma arquitetura, os recursos são avaliados pela Azure Policy para o incumprimento das definições políticas atribuídas. Para obter mais informações, veja [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Implementar

Para implantar a amostra de planta Azure Blueprints HIPAA HITRUST 9.2, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar um esquema novo a partir do exemplo
> - Marcar a cópia do exemplo como **Publicada**
> - Atribuir a cópia do esquema a uma subscrição já existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

### <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Primeiro, crie um esquema novo no ambiente utilizando o exemplo como ponto de partida, para implementar o esquema de exemplo.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Na página **Começar** à esquerda, selecione o botão **Criar** em _Criar um esquema_.

1. Encontre a amostra de planta **HIPAA HITRUST** em _Outras Amostras_ e selecione **Utilize esta amostra**.

1. Introduza as _Informações Básicas_ do esquema de exemplo:

   - **Nome da planta**: Forneça um nome para a sua cópia da amostra de planta HIPAA HITRUST 9.2.
   - **Localização de definição**: Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_, na parte superior da página, ou **Seguinte: Artefactos**, na parte inferior.

1. Reveja a lista de artefactos que compõem o esquema de exemplo. Muitos dos artefactos têm parâmetros que vamos definir mais tarde. Quando terminar de rever o esquema de exemplo, selecione **Guardar Rascunho**.

### <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

A cópia do esquema de exemplo está agora criada no seu ambiente. Está criada no modo **Rascunho** e tem de ser **Publicada** antes de poder ser atribuída e implementada. A cópia da amostra de planta pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode movê-la para longe do alinhamento com os controlos HIPAA HITRUST 9.2.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Publicar esquema**, na parte superior da página. Na página nova à direita, indique uma **Versão** para a cópia do esquema de exemplo. Esta propriedade é útil se fizer modificações mais tarde. Fornecer **notas de alteração** tais como "Primeira versão publicada a partir da amostra de planta HIPAA HITRUST 9.2." Em seguida, selecione **Publicar**, na parte inferior da página.

### <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Uma vez publicada com **sucesso** a cópia da amostra de plantas, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. É neste passo que são fornecidos os parâmetros que fazem com que cada implementação da cópia do esquema de exemplo seja única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Atribuir esquema**, na parte superior da página de definição do esquema.

1. Indique os valores dos parâmetros para a atribuição do esquema:

   - Noções básicas

     - **Subscrições**: Selecione uma ou mais das subscrições que estão no grupo de gestão a que guardou a sua cópia da amostra de planta. Se selecionar mais de uma subscrição, é criada uma atribuição para cada uma mediante a utilização dos parâmetros introduzidos.
     - **Nome da atribuição**: O nome é pré-povoado para si com base no nome da planta.
       Mude-o se necessário ou deixe-o como está.
     - **Localização**: Selecione uma região para a identidade gerida a criar. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, consulte [identidades geridas para recursos Azure.](../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Versão de definição de planta**: Escolha uma versão **publicada** da sua cópia da amostra de planta.

   - Atribuição de bloqueio

     Selecione a definição de bloqueio do esquema no seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a opção de identidade gerida _por defeito atribuída._

   - Parâmetros dos artefactos

     Os parâmetros definidos nesta secção aplicam-se ao artefacto no qual são definidos. Estes parâmetros são [parâmetros dinâmicos,](../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte [a tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Depois de introduzidos todos os parâmetros, selecione **Atribuir**, na parte inferior da página. A atribuição do esquema é criada e a implementação do artefacto inicia-se. A implantação leva cerca de uma hora. Para verificar o estado, abra a atribuição do esquema.

> [!WARNING]
> O serviço Azure Blueprints e os esquemas de exemplo incorporados são **gratuitos**. Os preços dos recursos do Azure são os [preços por produto](https://azure.microsoft.com/pricing/). Utilize a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para prever o custo da execução de recursos implementados por este esquema de exemplo.

### <a name="artifact-parameters-table"></a>Tabela de parâmetros dos artefactos

A tabela seguinte mostra uma lista dos parâmetros dos artefactos de esquema:

|Nome do artefacto |Nome do parâmetro |Descrição |
|---|---|---|
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |O acesso através da Internet face ao ponto final deve ser restringido |Permitir ou desativar regras de NSG excessivamente permissivas |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Contas: estado de conta de Convidado |Especifica se a conta local do Hóspede está desativada. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Os controlos de aplicações adaptativos devem ser ativados em máquinas virtuais |Permitir ou desativar a monitorização da lista de aplicações no Centro de Segurança Azure |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Permitir ligações simultâneas à Internet ou a um Domínio Windows |Especificar se impede que os computadores se conectem tanto a uma rede baseada em domínios como a uma rede baseada em não domínios ao mesmo tempo. Um valor de 0 permite ligações simultâneas, e um valor de 1 bloqueia-as. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |A API App só deve estar acessível em HTTPS V2 |Permitir ou desativar a monitorização da utilização do HTTPS na App V2 da API |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Nomes de aplicações (suporta wildcards) |Uma lista separada de pontos dos nomes das aplicações que devem ser instaladas. por exemplo , 'Microsoft SQL Server 2014 (64 bits); Microsoft Visual Studio Code' ou "Microsoft SQL Server 2014*" (para combinar qualquer aplicação que comece com 'Microsoft SQL Server 2014') |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Auditar termo de processo |Especifica se os eventos de auditoria são gerados quando um processo saiu. Recomendado para monitorizar a interrupção de processos críticos. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Auditoria acesso ilimitado à rede a contas de armazenamento |Permitir ou desativar a monitorização do acesso à rede à conta de armazenamento |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Auditoria: encerrar o sistema imediatamente se não conseguir registar as auditorias de segurança |Auditorias se o sistema for desligado quando não conseguir registar eventos de Segurança. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Impressões digitais de certificado |Uma lista separada de pontos de ordem e vírgula de impressões digitais de certificado que devem existir na loja de certificados Trusted Root (Cert:\LocalMachine\Root). por exemplo, THUMBPRINT1; IMPRESSÃO DIGITAL2; IMPRESSÃO DIGITAL3 |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Os registos de diagnóstico nas contas do Lote devem ser ativados |Ativar ou desativar a monitorização de registos de diagnóstico em contas de Lote |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Os registos de diagnóstico no Centro de Eventos devem ser ativados |Ativar ou desativar a monitorização de registos de diagnóstico nas contas do Event Hub |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Os registos de diagnóstico nos serviços de pesquisa devem ser ativados |Ativar ou desativar a monitorização de registos de diagnóstico no serviço de pesquisa Azure |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Os registos de diagnóstico em conjuntos de escala de máquina virtual devem ser ativados |Ativar ou desativar a monitorização de registos de diagnóstico em Tecido de Serviço |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |A encriptação do disco deve ser aplicada em máquinas virtuais |Ativar ou desativar a monitorização da encriptação do disco VM |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Ativar logons de hóspedes inseguros |Especifica se o cliente SMB permitirá logons de hóspedes inseguros para um servidor SMB. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais |Ativar ou desativar a monitorização da rede apenas no acesso a tempo |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |As portas de gestão devem ser fechadas nas suas máquinas virtuais |Ativar ou desativar a monitorização de portas de gestão abertas em Máquinas Virtuais |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |A MFA deve ser ativada em contas com permissões de escrita na sua subscrição |Ativar ou desativar a monitorização do MFA para contas com permissões de escrita na subscrição |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição |Ativar ou desativar a monitorização do MFA para contas com permissões do proprietário na subscrição |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Acesso de rede: caminhos de registo remotamente acessíveis |Especifica quais os caminhos de registo acessíveis através da rede, independentemente dos utilizadores ou grupos listados na lista de controlo de acesso (ACL) da chave de `winreg` registo. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Acesso à rede: Caminhos de registo e sub-caminhos acessíveis remotamente |Especifica quais os caminhos de registo e sub-caminhos acessíveis através da rede, independentemente dos utilizadores ou grupos listados na lista de controlo de acesso (ACL) da chave de `winreg` registo. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Acesso de rede: as partilhas que podem ser acedidas anonimamente |Especifica quais as partilhas de rede que podem ser acedidas por utilizadores anónimos. A configuração padrão para esta definição de política tem pouco efeito porque todos os utilizadores têm de ser autenticados antes de poderem aceder a recursos partilhados no servidor. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Consola de recuperação: Permitir cópia floppy e acesso a todas as unidades e todas as pastas |Especifica se deve disponibilizar o comando Recovery Console SET, que permite a definição de variáveis de ambiente da consola de recuperação. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Depuragem remota deve ser desligada para app API |Ativar ou desativar a monitorização da depuração remota para a App API |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Depuragem remota deve ser desligada para aplicação web |Ativar ou desativar a monitorização da depuração remota para a Web App |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Retenção necessária (em dias) para registos em contas de Lote |O período de retenção de registos de diagnóstico necessário em dias |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Retenção necessária (em dias) de registos no serviço de Pesquisa Azure |O período de retenção de registos de diagnóstico necessário em dias |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Retenção necessária (em dias) de registos nas contas do Event Hub |O período de retenção de registos de diagnóstico necessário em dias |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Nome do grupo de recursos para conta de armazenamento (deve existir) para implementar definições de diagnóstico para grupos de segurança de rede |O grupo de recursos em que a conta de armazenamento será criada. Este grupo de recursos já deve existir. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Role-Based Access Control (RBAC) deve ser usado nos serviços Kubernetes |Ativar ou desativar a monitorização dos Serviços Kubernetes sem o RBAC ativado |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |O protetor TDE de caso gerido pela SQL deve ser encriptado com a sua própria chave |Ative ou desative a monitorização da Encriptação de Dados Transparente (TDE) com o seu próprio suporte de chave. O TDE com o seu próprio suporte chave proporciona maior transparência e controlo sobre o Protetor TDE, maior segurança com um serviço externo apoiado pelo HSM e promoção da separação de direitos. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |O protetor TDE do servidor SQL deve ser encriptado com a sua própria chave |Ative ou desative a monitorização da Encriptação de Dados Transparente (TDE) com o seu próprio suporte de chave. O TDE com o seu próprio suporte chave proporciona maior transparência e controlo sobre o Protetor TDE, maior segurança com um serviço externo apoiado pelo HSM e promoção da separação de direitos. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Prefixo de conta de armazenamento para conta de armazenamento regional para implementar definições de diagnóstico para grupos de segurança de rede |Este prefixo será combinado com a localização do grupo de segurança da rede para formar o nome da conta de armazenamento criada. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |As atualizações do sistema em conjuntos de escala de máquinas virtuais devem ser instaladas |Ativar ou desativar conjuntos de escala de máquinas virtuais reportando de atualizações do sistema |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |As atualizações do sistema em conjuntos de escala de máquinas virtuais devem ser instaladas |Ativar ou desativar conjuntos de escala de máquinas virtuais reportando de atualizações do sistema |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Desligue a resolução de nomes multicast |Especifica se llMNR, um protocolo de resolução de nome secundário que transmite usando uma ligação multicast sobre uma sub-rede local numa única sub-rede, está ativado. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Máquinas virtuais devem ser migradas para novos recursos do Azure Resource Manager |Ativar ou desativar a monitorização de VMs de computação clássica |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquina virtual devem ser remediadas |Ativar ou desativar a escala de máquinas virtuais define a monitorização de vulnerabilidades de OS |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |As vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades |Permitir ou desativar a deteção de vulnerabilidades VM através de uma solução de avaliação de vulnerabilidades |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |A avaliação da vulnerabilidade deve ser ativada nas suas instâncias geridas pelo SQL |Audit SQL geriu casos que não têm verificações recorrentes de avaliação de vulnerabilidade ativadas. A avaliação de vulnerabilidades pode descobrir, rastrear e ajudá-lo a corrigir potenciais vulnerabilidades de base de dados. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Firewall do Windows (Domínio): Aplicar regras locais de firewall |Especifica se os administradores locais são autorizados a criar regras locais de firewall que se aplicam juntamente com as regras de firewall configuradas pela Política de Grupo para o perfil de Domínio. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Firewall do Windows (Domínio): Comportamento para ligações de saída |Especifica o comportamento das ligações de saída para o perfil de Domínio que não correspondem a uma regra de firewall de saída. O valor predefinido de 0 significa permitir ligações, e um valor de 1 significa bloquear ligações. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Firewall do Windows (Domínio): Comportamento para ligações de saída |Especifica o comportamento das ligações de saída para o perfil de Domínio que não correspondem a uma regra de firewall de saída. O valor predefinido de 0 significa permitir ligações, e um valor de 1 significa bloquear ligações. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Firewall do Windows (Domínio): Notificações de exibição |Especifica se o Windows Firewall com Segurança Avançada apresenta notificações ao utilizador quando um programa está bloqueado de receber ligações de entrada, para o perfil de Domínio. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Firewall do Windows (Domínio): Utilize as definições de perfil |Especifica se o Windows Firewall com Segurança Avançada utiliza as definições para o perfil de Domínio para filtrar o tráfego da rede. Se selecionar Off, o Windows Firewall com Segurança Avançada não utilizará nenhuma das regras de firewall ou regras de segurança de ligação para este perfil. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Firewall do Windows (Privado): Aplicar regras de segurança de ligação locais |Especifica se os administradores locais são autorizados a criar regras de segurança de ligação que se aplicam em conjunto com as regras de segurança de ligação configuradas pela Política de Grupo para o perfil privado. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Firewall do Windows (Privado): Aplicar regras locais de firewall |Especifica se os administradores locais são autorizados a criar regras locais de firewall que se aplicam em conjunto com as regras de firewall configuradas pela Política de Grupo para o perfil privado. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Firewall do Windows (Privado): Comportamento para ligações de saída |Especifica o comportamento das ligações de saída para o perfil Private que não correspondem a uma regra de firewall de saída. O valor predefinido de 0 significa permitir ligações, e um valor de 1 significa bloquear ligações. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Firewall do Windows (Privado): Notificações de exibição |Especifica se o Windows Firewall com Segurança Avançada apresenta notificações ao utilizador quando um programa está bloqueado de receber ligações de entrada, para o perfil Privado. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Firewall do Windows (Privado): Utilize as definições de perfil |Especifica se o Windows Firewall com Segurança Avançada utiliza as definições para o perfil Privado para filtrar o tráfego da rede. Se selecionar Off, o Windows Firewall com Segurança Avançada não utilizará nenhuma das regras de firewall ou regras de segurança de ligação para este perfil. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Firewall do Windows (Público): Aplicar regras de segurança de ligação locais |Especifica se os administradores locais são autorizados a criar regras de segurança de ligação que se aplicam em conjunto com as regras de segurança de ligação configuradas pela Política de Grupo para o perfil público. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Firewall do Windows (Público): Aplicar regras locais de firewall |Especifica se os administradores locais são autorizados a criar regras locais de firewall que se aplicam em conjunto com as regras de firewall configuradas pela Política de Grupo para o perfil público. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Firewall do Windows (Público): Comportamento para ligações de saída |Especifica o comportamento das ligações de saída para o perfil público que não correspondem a uma regra de firewall de saída. O valor predefinido de 0 significa permitir ligações, e um valor de 1 significa bloquear ligações. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Firewall do Windows (Público): Notificações de exibição |Especifica se o Windows Firewall com Segurança Avançada apresenta notificações ao utilizador quando um programa está bloqueado de receber ligações de entrada, para o perfil do Público. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Firewall do Windows (Público): Utilize as definições de perfis |Especifica se o Windows Firewall com Segurança Avançada utiliza as definições para o perfil público filtrar o tráfego da rede. Se selecionar Off, o Windows Firewall com Segurança Avançada não utilizará nenhuma das regras de firewall ou regras de segurança de ligação para este perfil. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Firewall do Windows: Domínio: Permitir resposta unicast |Especifica se o Windows Firewall com Segurança Avançada permite que o computador local receba respostas unicast às suas mensagens multicast ou de transmissão de saída; para o perfil de Domínio. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Firewall do Windows: Privado: Permitir resposta unicast |Especifica se o Windows Firewall com Segurança Avançada permite que o computador local receba respostas unicast às suas mensagens multicast ou de transmissão de saída; para o perfil privado. |
|Auditoria HiTRUST/HIPAA controla e implementa extensões VM específicas para apoiar os requisitos de auditoria |Firewall do Windows: Público: Permitir resposta unicast |Especifica se o Windows Firewall com Segurança Avançada permite que o computador local receba respostas unicast às suas mensagens multicast ou de transmissão de saída; para o perfil público. |

## <a name="next-steps"></a>Próximos passos

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
