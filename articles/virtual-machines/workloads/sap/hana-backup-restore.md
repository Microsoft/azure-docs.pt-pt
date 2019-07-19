---
title: Backup e restauração do HANA em SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Como executar o backup e a restauração do HANA em SAP HANA no Azure (instâncias grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 27f7a9b576263b97c251306c9817b85c31041739
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68312231"
---
# <a name="backup-and-restore"></a>Cópia de segurança e restauro

>[!IMPORTANT]
>Este artigo não é uma substituição para a documentação de administração do SAP HANA ou para as notas SAP. Esperamos que você tenha uma compreensão sólida e experiência em SAP HANA administração e operações, especialmente para backup, restauração, alta disponibilidade e recuperação de desastres. Neste artigo, são mostradas capturas de tela do SAP HANA Studio. O conteúdo, a estrutura e a natureza das telas das ferramentas de administração do SAP e as próprias ferramentas podem mudar de SAP HANA para liberação.

É importante que você exerça as etapas e os processos feitos em seu ambiente e com as versões e os lançamentos do HANA. Alguns processos descritos neste artigo são simplificados para um melhor entendimento geral. Eles não devem ser usados como etapas detalhadas para manuais de operação eventual. Se você quiser criar manuais de operação para suas configurações, teste e exerça seus processos e documente os processos relacionados às suas configurações específicas. 

Um dos aspectos mais importantes dos bancos de dados operacionais é protegê-los contra eventos catastróficos. A causa desses eventos pode ser qualquer coisa, desde desastres naturais até erros de usuário simples.

O backup de um banco de dados do, com a capacidade de restaurá-lo para qualquer ponto no tempo, como antes de alguém excluir dados críticos, permite a restauração para um estado o mais próximo possível da forma como era antes da interrupção.

Dois tipos de backups devem ser executados para alcançar a capacidade de restauração:

- Backups de banco de dados: Backups completos, incrementais ou diferenciais
- Backups de log de transações

Além dos backups completos de bancos de dados executados em um nível de aplicativo, você pode executar backups com instantâneos de armazenamento. Os instantâneos de armazenamento não substituem os backups de log de transações. Os backups de log de transações permanecem importantes para restaurar o banco de dados para um determinado ponto no tempo ou para esvaziar os logs de transações já confirmadas. Os instantâneos de armazenamento podem acelerar a recuperação fornecendo rapidamente uma imagem de avanço do banco de dados. 

SAP HANA no Azure (instâncias grandes) oferece duas opções de backup e restauração:

- **Faça você mesmo (DIY).** Depois de verificar se há espaço em disco suficiente, execute backups completos de banco de dados e log usando um dos seguintes métodos de backup em disco. Você pode fazer backup diretamente em volumes anexados às unidades de instância grande do HANA ou aos compartilhamentos NFS configurados em uma VM (máquina virtual) do Azure. No último caso, os clientes configuram uma VM do Linux no Azure, anexam o armazenamento do Azure à VM e compartilham o armazenamento por meio de um servidor NFS configurado nessa VM. Se você executar o backup em volumes que se anexam diretamente às unidades de instância grande do HANA, copie os backups para uma conta de armazenamento do Azure. Faça isso depois de configurar uma VM do Azure que exporta compartilhamentos NFS baseados no armazenamento do Azure. Você também pode usar um cofre de backup do Azure ou o armazenamento Cold do Azure. 

   Outra opção é usar uma ferramenta de proteção de dados de terceiros para armazenar os backups depois que eles são copiados para uma conta de armazenamento do Azure. A opção de backup DIY também pode ser necessária para os dados que você precisa armazenar por períodos de tempo mais longos para fins de conformidade e auditoria. Em todos os casos, os backups são copiados em compartilhamentos NFS representados por meio de uma VM e do armazenamento do Azure.

- **Funcionalidade de backup e restauração de infraestrutura.** Você também pode usar a funcionalidade de backup e restauração que a infraestrutura subjacente do SAP HANA no Azure (instâncias grandes) fornece. Essa opção atende à necessidade de backups e restaurações rápidas. O restante desta seção aborda a funcionalidade de backup e restauração oferecida com instâncias grandes do HANA. Esta seção também aborda a relação que o backup e a restauração têm para a funcionalidade de recuperação de desastres oferecida pelo HANA em instâncias grandes.

> [!NOTE]
>   A tecnologia de instantâneo usada pela infraestrutura subjacente do HANA em instâncias grandes tem uma dependência em SAP HANA instantâneos. Neste ponto, os instantâneos de SAP HANA não funcionam em conjunto com vários locatários de SAP HANA contêineres de banco de dados multilocatário. Se apenas um locatário for implantado, SAP HANA instantâneos funcionam e você pode usar esse método.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Usar instantâneos de armazenamento de SAP HANA no Azure (instâncias grandes)

A infraestrutura de armazenamento subjacente SAP HANA no Azure (instâncias grandes) dá suporte a instantâneos de armazenamento de volumes. Há suporte para backup e restauração de volumes, com as seguintes considerações:

- Em vez de backups de banco de dados completos, os instantâneos de volume de armazenamento são feitos com frequência.
- Quando um instantâneo é disparado sobre/Hana/data e/Hana/Shared, que inclui/usr/SAP, volumes, a tecnologia de instantâneo inicia um instantâneo de SAP HANA antes de executar o instantâneo de armazenamento. Esse SAP HANA instantâneo é o ponto de instalação para restaurações de log eventual após a recuperação do instantâneo de armazenamento. Para que um instantâneo do HANA seja bem-sucedido, você precisa de uma instância ativa do HANA. Em um cenário de HSR, um instantâneo de armazenamento não tem suporte em um nó secundário atual em que um instantâneo do HANA não pode ser executado.
- Depois que o instantâneo de armazenamento é executado com êxito, o instantâneo de SAP HANA é excluído.
- Os backups de log de transações são feitos com frequência e armazenados no volume/Hana/logbackups ou no Azure. Você pode disparar o volume/Hana/logbackups que contém os backups de log de transações para tirar um instantâneo separadamente. Nesse caso, você não precisa executar um instantâneo do HANA.
- Se você precisar restaurar um banco de dados para um determinado ponto no tempo, para uma interrupção de produção, solicite que Microsoft Azure suporte ou SAP HANA no Azure Restore para um determinado instantâneo de armazenamento. Um exemplo é uma restauração planejada de um sistema de área restrita para seu estado original.
- O instantâneo de SAP HANA que está incluído no instantâneo de armazenamento é um ponto de deslocamento para a aplicação de backups de log de transações que foram executados e foram armazenados após o instantâneo de armazenamento ter sido tirado.
- Esses backups de log de transações são usados para restaurar o banco de dados de volta para um determinado ponto no tempo.

Você pode executar instantâneos de armazenamento direcionados a três classes de volumes:

- Um instantâneo combinado sobre/Hana/data e/Hana/Shared, que inclui/usr/SAP. Esse instantâneo requer a criação de um instantâneo de SAP HANA como preparação para o instantâneo de armazenamento. O instantâneo de SAP HANA garante que o banco de dados esteja em um estado consistente de um ponto de vista de armazenamento. Para o processo de restauração, esse é um ponto a ser configurado em.
- Um instantâneo separado sobre/Hana/logbackups.
- Uma partição do sistema operacional.

Para obter os scripts de instantâneo e a documentação mais recentes, consulte [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1). Ao baixar o pacote de script de instantâneo do [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1), você obtém três arquivos. Um dos arquivos é documentado em um PDF para a funcionalidade fornecida. Depois de baixar o conjunto de ferramentas, siga as instruções em "obter as ferramentas de instantâneo".

## <a name="storage-snapshot-considerations"></a>Considerações de instantâneo de armazenamento

>[!NOTE]
>Os instantâneos de armazenamento consomem espaço de armazenamento alocado para as unidades de instância grande do HANA. Considere os seguintes aspectos do agendamento de instantâneos de armazenamento e quantos instantâneos de armazenamento devem ser mantidos. 

A mecânica específica de instantâneos de armazenamento para SAP HANA no Azure (instâncias grandes) inclui:

- Um instantâneo de armazenamento específico no momento em que ele é tomado consome pouco armazenamento.
- À medida que o conteúdo de dados é alterado e o conteúdo em SAP HANA arquivos de dados é alterado no volume de armazenamento, o instantâneo precisa armazenar o conteúdo do bloco original e as alterações de dados.
- Como resultado, o instantâneo de armazenamento aumenta em tamanho. Quanto mais tempo o instantâneo existir, maior será o instantâneo de armazenamento.
- Quanto mais alterações forem feitas no volume de banco de dados SAP HANA durante o tempo de vida de um instantâneo de armazenamento, maior será o consumo de espaço do instantâneo de armazenamento.

SAP HANA no Azure (instâncias grandes) vem com tamanhos de volume fixo para os dados de SAP HANA e volumes de log. Executar instantâneos desses volumes consome em seu espaço de volume. Você precisa:

- Determine quando agendar instantâneos de armazenamento.
- Monitore o consumo de espaço dos volumes de armazenamento. 
- Gerencie o número de instantâneos que você armazena. 

Você pode desabilitar os instantâneos de armazenamento ao importar massas de dados ou executar outras alterações significativas no banco de dado do HANA. 


As seções a seguir fornecem informações para a execução desses instantâneos e incluem recomendações gerais:

- Embora o hardware possa sustentar 255 instantâneos por volume, você deseja ficar bem abaixo desse número. A recomendação é de 250 ou menos.
- Antes de executar instantâneos de armazenamento, monitore e mantenha o controle do espaço livre.
- Reduza o número de instantâneos de armazenamento com base no espaço livre. Você pode reduzir o número de instantâneos que você mantém ou pode estender os volumes. Você pode solicitar armazenamento adicional em unidades de 1 terabyte.
- Durante as atividades, como a movimentação de dados em SAP HANA com as ferramentas de migração de plataforma SAP (R3load) ou a restauração de bancos de SAP HANA de backups, desabilite os instantâneos de armazenamento no volume/Hana/Data. 
- Durante reorganizações maiores de SAP HANA tabelas, evite instantâneos de armazenamento, se possível.
- Os instantâneos de armazenamento são um pré-requisito para aproveitar os recursos de recuperação de desastre do SAP HANA no Azure (instâncias grandes).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Pré-requisitos para usar instantâneos de armazenamento de autoatendimento

Para certificar-se de que o script de instantâneo seja executado com êxito, verifique se o Perl está instalado no sistema operacional Linux no servidor HANA em instâncias grandes. O Perl vem pré-instalado em sua unidade de instância grande do HANA. Para verificar a versão do Perl, use o seguinte comando:

`perl -v`

![A chave pública é copiada executando este comando](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Configurar instantâneos de armazenamento

Para configurar instantâneos de armazenamento com instâncias grandes do HANA, siga estas etapas.
1. Verifique se o Perl está instalado no sistema operacional Linux no servidor HANA em instâncias grandes.
1. Modifique a configuração\_/etc/ssh/SSH para adicionar a linha _Macs HMAC-SHA1_.
1. Crie uma conta de usuário de backup SAP HANA no nó mestre para cada instância de SAP HANA que você executar, se aplicável.
1. Instale o SAP HANA cliente HDB em todos os servidores SAP HANA em Instâncias Grandes.
1. No primeiro SAP HANA em Instâncias Grandes servidor de cada região, crie uma chave pública para acessar a infraestrutura de armazenamento subjacente que controla a criação de instantâneos.
1. Copie os scripts e o arquivo de configuração do [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1) para o local do **hdbsql** na instalação do SAP Hana.
1. Modifique o arquivo *arquivo hanabackupdetails. txt* conforme necessário para as especificações apropriadas do cliente.

Obtenha os scripts de instantâneo e a documentação mais recentes do [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1). Para as etapas listadas anteriormente, consulte [Microsoft snapshot Tools for SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf).

### <a name="consideration-for-mcod-scenarios"></a>Considerações para cenários de MCOD
Se você executar um [cenário de MCOD](https://launchpad.support.sap.com/#/notes/1681092) com várias instâncias de SAP Hana em uma unidade de instância grande do Hana, você terá volumes de armazenamento separados provisionados para cada uma das instâncias de SAP Hana. Para obter mais informações sobre MDC e outras considerações, consulte "coisas importantes a serem lembradas" em [Microsoft snapshot Tools for SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Passo 1: Instalar o SAP HANA cliente HDB

O sistema operacional Linux instalado em SAP HANA no Azure (instâncias grandes) inclui as pastas e os scripts necessários para executar SAP HANA instantâneos de armazenamento para fins de backup e recuperação de desastre. Verifique se há versões mais recentes no [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1). A versão de lançamento mais recente dos scripts é 4,1. Scripts diferentes podem ter versões secundárias diferentes na mesma versão principal.

É sua responsabilidade instalar o SAP HANA cliente HDB nas unidades do SAP HANA em instâncias grandes enquanto você instala o SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Passo 2: Alterar a configuração\_de/etc/ssh/SSH

Esta etapa é descrita em "habilitar a comunicação com o armazenamento" no [Microsoft snapshot Tools para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf).


### <a name="step-3-create-a-public-key"></a>Passo 3: Criar uma chave pública

Para habilitar o acesso às interfaces de instantâneo de armazenamento do seu locatário de instância grande do HANA, estabeleça um procedimento de entrada por meio de uma chave pública. 

No primeiro SAP HANA no servidor do Azure (instâncias grandes) em seu locatário, crie uma chave pública para acessar a infraestrutura de armazenamento. Com uma chave pública, uma senha não é necessária para entrar nas interfaces de instantâneo de armazenamento. Você também não precisa manter credenciais de senha com uma chave pública. 

Para gerar uma chave pública, consulte "habilitar a comunicação com o armazenamento" nas [ferramentas de instantâneo da Microsoft para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf).


### <a name="step-4-create-an-sap-hana-user-account"></a>Passo 4: Criar uma conta de usuário do SAP HANA

Para iniciar a criação de instantâneos de SAP HANA, crie uma conta de usuário no SAP HANA que os scripts de instantâneo de armazenamento possam usar. Crie uma conta de usuário SAP HANA no SAP HANA Studio para essa finalidade. O usuário deve ser criado sob o SYSTEMDB e *não* no banco de dados Sid para MDC. No ambiente de contêiner único, o usuário é criado no banco de dados de locatário. Essa conta deve ter privilégios de **administrador de backup** e de **leitura de catálogo** . 

Para configurar e usar uma conta de usuário, consulte "habilitar a comunicação com o SAP HANA" no [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Passo 5: Autorizar a conta de usuário do SAP HANA

Nesta etapa, você autoriza a conta de usuário SAP HANA que você criou para que os scripts não precisem enviar senhas em tempo de execução. O comando `hdbuserstore` SAP Hana permite a criação de uma SAP Hana chave de usuário. A chave é armazenada em um ou mais nós de SAP HANA. A chave de usuário permite que o usuário acesse SAP HANA sem precisar gerenciar senhas de dentro do processo de script. O processo de script é discutido posteriormente neste artigo.

>[!IMPORTANT]
>Execute esses comandos de configuração com o mesmo contexto de usuário em que os comandos de instantâneo são executados. Caso contrário, os comandos de instantâneo não funcionarão corretamente.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Passo 6: Obter os scripts de instantâneo, configurar os instantâneos e testar a configuração e a conectividade

Baixe a versão mais recente dos scripts do [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1). A maneira como os scripts são instalados é alterada com a versão 4,1 dos scripts. Para obter mais informações, consulte "habilitar a comunicação com o SAP HANA" em [Microsoft snapshot Tools para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf).

Para obter a seqüência exata de comandos, consulte "instalação fácil de ferramentas de instantâneo (padrão)" no [Microsoft snapshot Tools para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf). Recomendamos o uso da instalação padrão. 

Para atualizar da versão 3. x para 4,1, consulte "atualizar uma instalação existente" nas [ferramentas de instantâneo da Microsoft para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf). Para desinstalar o conjunto de ferramentas 4,1, consulte "desinstalação das ferramentas de instantâneo" em [Microsoft snapshot Tools para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf).

Não se esqueça de executar as etapas descritas em "concluir a instalação das ferramentas de instantâneo" nas [ferramentas de instantâneo da Microsoft para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf).

A finalidade dos diferentes scripts e arquivos como eles foram instalados é descrita em "o que são essas ferramentas de instantâneo?" no [Microsoft snapshot Tools para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf).

Antes de configurar as ferramentas de instantâneo, certifique-se de que você também configurou locais e configurações de backup do HANA corretamente. Para obter mais informações, consulte "configuração de SAP HANA" no [Microsoft snapshot Tools para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf).

A configuração do conjunto de ferramentas de instantâneo é descrita em "config file-HANABackupCustomerDetails. txt" nas [ferramentas de instantâneo da Microsoft para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf).

#### <a name="test-connectivity-with-sap-hana"></a>Testar a conectividade com SAP HANA

Depois de colocar todos os dados de configuração no arquivo *HANABackupCustomerDetails. txt* , verifique se as configurações estão corretas para os dados da instância do Hana. Use o script `testHANAConnection`, que é independente de uma configuração SAP Hana escalar verticalmente ou escalar horizontalmente.

Para obter mais informações, consulte "verificar a conectividade com o SAP HANA-testHANAConnection" nas [ferramentas de instantâneo da Microsoft para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf).

#### <a name="test-storage-connectivity"></a>Testar conectividade de armazenamento

A próxima etapa de teste é verificar a conectividade com o armazenamento com base nos dados que você colocou no arquivo de configuração *HANABackupCustomerDetails. txt* . Em seguida, execute um instantâneo de teste. Antes de executar o `azure_hana_backup` comando, você deve executar este teste. Para obter a sequência de comandos para este teste, consulte "verificar a conectividade com armazenamento-testStorageSnapshotConnection" no [Microsoft snapshot Tools para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf).

Após uma entrada bem-sucedida nas interfaces de máquina virtual de armazenamento, o script continua com a fase 2 e cria um instantâneo de teste. A saída é mostrada aqui para uma configuração de expansão de três nós de SAP HANA.

Se o instantâneo de teste for executado com êxito com o script, você poderá agendar os instantâneos de armazenamento reais. Se ele não for bem-sucedido, investigue os problemas antes de prosseguir. O instantâneo de teste deve ficar em frente até que os primeiros instantâneos reais sejam concluídos.


### <a name="step-7-perform-snapshots"></a>Passo 7: Executar instantâneos

Quando as etapas de preparação forem concluídas, você poderá começar a configurar e agendar os instantâneos de armazenamento reais. O script a ser agendado funciona com SAP HANA configurações de expansão e expansão. Para execução periódica e regular do script de backup, agende o script usando o utilitário cron. 

Para obter a sintaxe e a funcionalidade exatas do comando, consulte "executar backup de instantâneo-azure_hana_backup" em [Microsoft snapshot Tools para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf). 

Quando o script `azure_hana_backup` é executado, ele cria o instantâneo de armazenamento nas três fases a seguir:

1. Ele executa um instantâneo SAP HANA.
1. Ele executa um instantâneo de armazenamento.
1. Ele remove o instantâneo de SAP HANA que foi criado antes da execução do instantâneo de armazenamento.

Para executar o script, chame-o da pasta executável HDB para a qual ele foi copiado. 

O período de retenção é administrado com o número de instantâneos que são enviados como um parâmetro quando você executa o script. A quantidade de tempo coberta pelos instantâneos de armazenamento é uma função do período de execução e do número de instantâneos enviados como um parâmetro quando o script é executado. 

Se o número de instantâneos que são mantidos exceder o número nomeado como um parâmetro na chamada do script, o instantâneo de armazenamento mais antigo do mesmo rótulo será excluído antes de um novo instantâneo ser executado. O número que você dá como o último parâmetro da chamada é o número que você pode usar para controlar o número de instantâneos que são mantidos. Com esse número, você também pode controlar, indiretamente, o espaço em disco usado para instantâneos. 


## <a name="snapshot-strategies"></a>Estratégias de instantâneo
A frequência de instantâneos para os diferentes tipos depende se você usa a funcionalidade de recuperação de desastre de instância grande do HANA. Essa funcionalidade depende de instantâneos de armazenamento, que podem exigir recomendações especiais para a frequência e os períodos de execução dos instantâneos de armazenamento. 

Nas considerações e recomendações a seguir, pressupõe-se que você *não* use a funcionalidade de recuperação de desastres que o Hana em instâncias grandes oferece. Em vez disso, você usa os instantâneos de armazenamento para ter backups e ser capaz de fornecer recuperação pontual nos últimos 30 dias. Devido às limitações do número de instantâneos e espaço, considere os seguintes requisitos:

- O tempo de recuperação para recuperação pontual.
- O espaço usado.
- Os objetivos de ponto de recuperação e tempo de recuperação para recuperação em potencial de um desastre.
- A eventual execução de backups completos de bancos de dados do HANA em discos. Sempre que um backup de banco de dados completo em discos ou a interface **BACKINT** é executada, a execução dos instantâneos de armazenamento falha. Se você planeja executar backups completos de bancos de dados sobre instantâneos de armazenamento, certifique-se de que a execução dos instantâneos de armazenamento esteja desabilitada durante esse tempo.
- O número de instantâneos por volume, que é limitado a 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Se você não usar a funcionalidade de recuperação de desastres do HANA em instâncias grandes, o período de instantâneo será menos frequente. Nesses casos, execute os instantâneos combinados em/Hana/data e/Hana/Shared, que inclui/usr/SAP, em períodos de 12 horas ou 24 horas. Mantenha os instantâneos por um mês. O mesmo é verdadeiro para os instantâneos do volume de backup de log. A execução de SAP HANA backups de log de transações no volume de backup de log ocorre em períodos de 5 minutos a 15 minutos.

Os instantâneos de armazenamento agendados são mais bem executados usando cron. Use o mesmo script para todos os backups e necessidades de recuperação de desastre. Modifique as entradas do script para que correspondam aos vários tempos de backup solicitados. Esses instantâneos são todos agendados de modo diferente em cron, dependendo de seu tempo de execução. Pode ser por hora, a cada 12 horas, diariamente ou semanalmente. 

O exemplo a seguir mostra uma agenda cron em/etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
No exemplo anterior, um instantâneo combinado por hora aborda os volumes que contêm o/Hana/data e o/hana/shared/SID, que inclui/usr/SAP, locais. Use esse tipo de instantâneo para uma recuperação pontual mais rápida nos últimos dois dias. Também há um instantâneo diário nesses volumes. Portanto, você tem dois dias de cobertura por instantâneos por hora, além de quatro semanas de cobertura por instantâneos diários. O backup do volume de log de transações também é feito diariamente. Esses backups são mantidos por quatro semanas. 

Como você vê na terceira linha do crontab, o backup do log de transações do HANA está agendado para ser executado a cada 5 minutos. As horas de início dos trabalhos cron diferentes que executam instantâneos de armazenamento são escalonadas. Dessa forma, os instantâneos não são executados de uma só vez em um determinado ponto no tempo. 

No exemplo a seguir, você executa um instantâneo combinado que abrange os volumes que contêm/Hana/data e/hana/shared/SID, que inclui/usr/SAP, localizações por hora. Você mantém esses instantâneos por dois dias. Os instantâneos dos volumes de backup de log de transações são executados em uma base de 5 minutos e são mantidos por quatro horas. Como antes, o backup do arquivo de log de transações do HANA está agendado para ser executado a cada 5 minutos. 

O instantâneo do volume de backup de log de transações é executado com um atraso de 2 minutos após o backup do log de transações ter sido iniciado. Em circunstâncias normais, o backup do log de transações SAP HANA é concluído dentro desses 2 minutos. Como antes, o volume que contém o LUN de inicialização é submetido a backup uma vez por dia por um instantâneo de armazenamento e é mantido por quatro semanas.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

O gráfico a seguir ilustra as sequências do exemplo anterior. O LUN de inicialização é excluído.

![Relação entre backups e instantâneos](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA executa gravações regulares no volume/Hana/log para documentar as alterações confirmadas no banco de dados. Regularmente, SAP HANA grava um salvamento no volume/Hana/Data. Conforme especificado no crontab, um backup de log de transações SAP HANA é executado a cada 5 minutos. 

Você também verá que um instantâneo de SAP HANA é executado a cada hora como resultado do acionamento de um instantâneo de armazenamento combinado nos volumes/Hana/data e/hana/shared/SID. Depois que o instantâneo do HANA for bem-sucedidos, o instantâneo de armazenamento combinado será executado. Conforme instruído no crontab, o instantâneo de armazenamento no volume/Hana/logbackup é executado a cada 5 minutos, cerca de 2 minutos após o backup de log de transações do HANA.

> 

>[!IMPORTANT]
> O uso de instantâneos de armazenamento para backups SAP HANA é valioso somente quando os instantâneos são executados em conjunto com SAP HANA backups de log de transações. Esses backups de log de transações precisam cobrir os períodos de tempo entre os instantâneos de armazenamento. 

Se você tiver definido um compromisso com os usuários de uma recuperação pontual de 30 dias, precisará:

- Acesse um instantâneo de armazenamento combinado sobre/Hana/data e/hana/shared/SID que é de 30 dias de idade, em casos extremos. 
- Ter backups de log de transações contíguos que cobrem o tempo entre qualquer um dos instantâneos de armazenamento combinados. Portanto, o instantâneo mais antigo do volume de backup do log de transações precisa ter 30 dias. Esse não é o caso se você copiar os backups de log de transações para outro compartilhamento NFS localizado no armazenamento do Azure. Nesse caso, você pode efetuar pull de backups de log de transações antigos desse compartilhamento NFS.

Para se beneficiar de instantâneos de armazenamento e replicação eventual de armazenamento de backups de log de transações, altere o local para o qual SAP HANA grava os backups de log de transações. Você pode fazer essa alteração no HANA Studio. 

Embora SAP HANA faça backup de segmentos de log completos automaticamente, especifique um intervalo de backup de log para ser determinístico. Isso é especialmente verdadeiro quando você usa a opção de recuperação de desastres porque geralmente deseja executar backups de log com um período determinístico. No caso a seguir, 15 minutos é definido como o intervalo de backup de log.

![Agendar SAP HANA logs de backup no SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Você também pode escolher backups que são mais frequentes do que a cada 15 minutos. Uma configuração mais frequente geralmente é usada em conjunto com a funcionalidade de recuperação de desastres do HANA em instâncias grandes. Alguns clientes executam backups de log de transações a cada 5 minutos.

Se o banco de dados nunca tiver sido submetido a backup, a etapa final será executar um backup de banco de dados baseado em arquivo para criar uma única entrada de backup que deve existir no catálogo de backup. Caso contrário, SAP HANA não poderá iniciar os backups de log especificados.

![Fazer um backup baseado em arquivo para criar uma única entrada de backup](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Após a execução de seus primeiros instantâneos de armazenamento com êxito, exclua o instantâneo de teste executado na etapa 6. Para obter mais informações, consulte "remover instantâneos de teste-removeTestStorageSnapshot" em [Microsoft snapshot Tools para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitorar o número e o tamanho dos instantâneos no volume do disco

Em um volume de armazenamento específico, você pode monitorar o número de instantâneos e o consumo de armazenamento desses instantâneos. O `ls` comando não mostra o diretório ou os arquivos do instantâneo. O comando `du` do sistema operacional Linux mostra detalhes sobre esses instantâneos de armazenamento porque eles são armazenados nos mesmos volumes. Use o comando com as seguintes opções:

- `du –sh .snapshot`: Essa opção fornece um total de todos os instantâneos no diretório do instantâneo.
- `du –sh --max-depth=1`: Essa opção lista todos os instantâneos que são salvos na pasta **. snapshot** e o tamanho de cada instantâneo.
- `du –hc`: Essa opção fornece o tamanho total usado por todos os instantâneos.

Use esses comandos para certificar-se de que os instantâneos que são criados e armazenados não consumam todo o armazenamento nos volumes.

>[!NOTE]
>Os instantâneos do LUN de inicialização não são visíveis com os comandos anteriores.

### <a name="get-details-of-snapshots"></a>Obter detalhes de instantâneos
Para obter mais detalhes sobre instantâneos, use o script `azure_hana_snapshot_details`. Você pode executar esse script em qualquer local se houver um servidor ativo no local de recuperação de desastre. O script fornece a seguinte saída, dividida por cada volume que contém instantâneos: 
   * O tamanho do total de instantâneos em um volume
   * Os seguintes detalhes em cada instantâneo nesse volume: 
      - Nome do instantâneo 
      - Tempo de criação 
      - Tamanho do instantâneo
      - Frequência do instantâneo
      - ID de backup do HANA associada a esse instantâneo, se relevante

Para obter a sintaxe do comando e das saídas, consulte "listar instantâneos-azure_hana_snapshot_details" em [Microsoft snapshot Tools para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Reduzir o número de instantâneos em um servidor

Conforme explicado anteriormente, você pode reduzir o número de determinados rótulos de instantâneos que você armazena. Os dois últimos parâmetros do comando para iniciar um instantâneo são o rótulo e o número de instantâneos que você deseja reter.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

No exemplo anterior, o rótulo do instantâneo é **dailyhana**. O número de instantâneos com este rótulo a ser mantido é **28**. Ao responder ao consumo de espaço em disco, talvez você queira reduzir o número de instantâneos armazenados. Uma maneira fácil de reduzir o número de instantâneos para 15, por exemplo, é executar o script com o último parâmetro definido como **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Se você executar o script com essa configuração, o número de instantâneos, que inclui o novo instantâneo de armazenamento, será 15. Os 15 instantâneos mais recentes são mantidos e os 15 instantâneos mais antigos são excluídos.

 >[!NOTE]
 > Esse script reduz o número de instantâneos somente se houver instantâneos mais de uma hora. O script não exclui instantâneos com menos de uma hora de idade. Essas restrições estão relacionadas à funcionalidade opcional de recuperação de desastre oferecida.

Se você não deseja mais manter um conjunto de instantâneos com o prefixo de backup **dailyhana** nos exemplos de sintaxe, execute o script com **0** como o número de retenção. Todos os instantâneos que correspondem a esse rótulo são removidos. A remoção de todos os instantâneos pode afetar os recursos da funcionalidade de recuperação de desastres de instâncias grandes do HANA.

Uma segunda opção para excluir instantâneos específicos é usar o script `azure_hana_snapshot_delete`. Esse script foi criado para excluir um instantâneo ou conjunto de instantâneos usando a ID de backup do HANA, conforme encontrado no HANA Studio ou pelo próprio nome do instantâneo. Atualmente, a ID de backup está vinculada somente aos instantâneos criados para o tipo de instantâneo do **Hana** . Backups de instantâneo do tipo **logs** e **inicialização** não executam um instantâneo SAP Hana, portanto, não há nenhuma ID de backup a ser encontrada para esses instantâneos. Se o nome do instantâneo for inserido, ele procurará todos os instantâneos nos diferentes volumes que correspondem ao nome do instantâneo inserido. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Para obter mais informações sobre o script, consulte "excluir um instantâneo-azure_hana_snapshot_delete" nas [ferramentas de instantâneo da Microsoft para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf).

Execute o script como **raiz**do usuário.

>[!IMPORTANT]
>Se houver dados que existem somente no instantâneo que você planeja excluir, depois que o instantâneo for excluído, esses dados serão perdidos para sempre.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Restauração em nível de arquivo de um instantâneo de armazenamento

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Para os tipos de instantâneo **Hana** e **logs**, você pode acessar os instantâneos diretamente nos volumes no diretório **. snapshot** . Há um subdiretório para cada um dos instantâneos. Copie cada arquivo no estado em que estava no ponto do instantâneo desse subdiretório na estrutura de diretório real. 

Na versão atual do script, não há *nenhum* script de restauração fornecido para a restauração de instantâneo como autoatendimento. A restauração de instantâneo pode ser executada como parte dos scripts de recuperação de desastre de autoatendimento no site de recuperação de desastre durante o failover. Para restaurar um instantâneo desejado dos instantâneos disponíveis existentes, você deve entrar em contato com a equipe de operações da Microsoft abrindo uma solicitação de serviço.

>[!NOTE]
>A restauração de arquivo único não funciona para instantâneos do LUN de inicialização independentemente do tipo das unidades do SAP HANA em instâncias grandes. O diretório **. snapshot** não é exposto no LUN de inicialização. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Recuperar para o instantâneo mais recente do HANA

Em um cenário de produção, o processo de recuperação de um instantâneo de armazenamento pode ser iniciado como um incidente do cliente com suporte a Microsoft Azure. É uma questão de alta urgência se os dados foram excluídos em um sistema de produção e a única maneira de recuperá-los é restaurar o banco de dados de produção.

Em uma situação diferente, uma recuperação pontual pode ser baixa urgência e dias planejados com antecedência. Você pode planejar essa recuperação com SAP HANA no Azure em vez de gerar um sinalizador de alta prioridade. Por exemplo, você pode planejar a atualização do software SAP aplicando um novo pacote de aprimoramento. Em seguida, você precisa reverter para um instantâneo que representa o estado antes da atualização do pacote de aprimoramento.

Antes de enviar a solicitação, você precisa se preparar. A SAP HANA na equipe do Azure pode manipular a solicitação e fornecer os volumes restaurados. Depois disso, você restaura o banco de dados do HANA com base nos instantâneos.

Para as possibilidades de obter um instantâneo restaurado com o novo conjunto de ferramentas, consulte "como restaurar um instantâneo" no [Guia de recuperação manual para SAP Hana no Azure de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Para se preparar para a solicitação, siga estas etapas.

1. Decida qual instantâneo restaurar. Somente o volume Hana/data é restaurado, a menos que você instrua o contrário. 

1. Desligue a instância do HANA.

   ![Desligar a instância do HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Desmonte os volumes de dados em cada nó do banco de dado do HANA. Se os volumes de dados ainda estiverem montados no sistema operacional, a restauração do instantâneo falhará.

   ![Desmonte os volumes de dados em cada nó do banco de dado HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Abra uma solicitação de suporte do Azure e inclua instruções sobre a restauração de um instantâneo específico:

   - Durante a restauração: SAP HANA no serviço do Azure pode pedir que você participe de uma chamada de conferência para coordenar, verificar e confirmar se o instantâneo de armazenamento correto foi restaurado. 

   - Após a restauração: SAP HANA no serviço do Azure notifica você quando o instantâneo de armazenamento é restaurado.

1. Após a conclusão do processo de restauração, monte novamente todos os volumes de dados.

   ![Remontar todos os volumes de dados](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Outra possibilidade de obter, por exemplo, SAP HANA arquivos de dados recuperados de um instantâneo de armazenamento, está documentada na etapa 7 no [Guia de recuperação manual para SAP Hana no Azure de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Para restaurar de um backup de instantâneo, consulte [Guia de recuperação manual para SAP Hana no Azure de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf). 

>[!Note]
>Se o instantâneo foi restaurado pelas operações da Microsoft, você não precisa fazer a etapa 7.


### <a name="recover-to-another-point-in-time"></a>Recuperar em outro ponto no tempo
Para restaurar para um determinado ponto no tempo, consulte "recuperar o banco de dados para o seguinte ponto no tempo" no [Guia de recuperação manual para SAP Hana no Azure de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf). 


## <a name="next-steps"></a>Passos Seguintes
- Consulte [princípios e preparação de recuperação de desastre](hana-concept-preparation.md).
