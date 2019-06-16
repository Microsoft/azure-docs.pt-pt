---
title: HANA backup e restauração no SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Como fazer cópia de segurança do HANA e restaurar no SAP HANA no Azure (instâncias grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 21232e5a678d6deed920e57cd0433a3b85ca4fdc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64987904"
---
# <a name="backup-and-restore"></a>Cópia de segurança e restauro

>[!IMPORTANT]
>Este artigo não é um substituto para a documentação de administração do SAP HANA ou SAP Notes. Esperamos que tenha uma compreensão sólida do e experiência em administração de SAP HANA e operações, especialmente para cópia de segurança, restauro, elevada disponibilidade e recuperação após desastre. Neste artigo, capturas de ecrã do SAP HANA Studio são apresentadas. Conteúdo, estrutura e a natureza das telas de ferramentas de administração do SAP e das ferramentas propriamente ditas podem ser alteradas a partir do SAP HANA versão para versão.

É importante que exerce o etapas e os processos executados no seu ambiente e com as suas versões do HANA e versões. Alguns processos descritos neste artigo são simplificados para uma melhor compreensão geral. Eles não são indicados para uso como obter os passos detalhados para handbooks operação eventual. Se quiser criar handbooks de operação para suas configurações, testar e exercer os seus processos e os processos relacionados com suas configurações específicas de documentos. 

Um dos aspectos mais importantes de bases de dados operacionais é protegê-los contra eventos catastróficos. A causa destes eventos pode ser qualquer coisa, desde desastres naturais para erros de usuário simples.

Cópia de segurança de uma base de dados, com a capacidade de restaurá-lo a qualquer ponto no tempo, como antes de alguém eliminados dados críticos, permite que o restauro para um Estado que está mais próximo possível para a forma como ele foi antes da interrupção.

Dois tipos de cópias de segurança devem ser executados para atingir a capacidade de restaurar:

- Cópias de segurança da base de dados: Cópias de segurança completas, incrementais ou diferenciais
- Backups de log de transação

Para além das cópias de segurança de base de dados completo realizadas em nível de aplicativo, pode executar cópias de segurança com instantâneos de armazenamento. Instantâneos de armazenamento não substituem backups de log de transação. Backups de log de transação permanecem importantes para restaurar a base de dados para um determinado ponto no tempo ou para esvaziar os registos de transações consolidadas já. Instantâneos de armazenamento podem acelerar a recuperação ao fornecer rapidamente uma imagem de roll-forward da base de dados. 

SAP HANA no Azure (instâncias grandes) oferece duas opções de cópia de segurança e restauro:

- **Fazê-lo por conta própria (DIY).** Depois de efetuar-se de que existe espaço suficiente em disco, execute a base de dados completa e backups de log utilizando um dos seguintes métodos de cópia de segurança de disco. Pode criar cópias de segurança diretamente para os volumes anexados para as unidades de instância grande do HANA ou para as partilhas NFS são configuradas numa máquina virtual do Azure (VM). No último caso, os clientes a configurar uma VM do Linux no Azure, anexar o armazenamento do Azure para a VM e partilhe o armazenamento através de um servidor NFS configurado nesta VM. Se efetuar a cópia de segurança face a volumes que anexar diretamente às unidades de instância grande do HANA, copie as cópias de segurança para uma conta de armazenamento do Azure. Opte por fazê-lo Depois de configurar uma VM do Azure que exporta as partilhas NFS baseiam-se no armazenamento do Azure. Também pode utilizar um cofre de cópia de segurança do Azure ou o armazenamento de esporádico do Azure. 

   Outra opção é usar uma ferramenta de proteção de dados de terceiros para armazenar as cópias de segurança depois de estarem copiados para uma conta de armazenamento do Azure. A opção de cópia de segurança faça mesmo também poderá ser necessária para os dados que precise armazenar por períodos mais longos de tempo para fins de auditoria e conformidade. Em todos os casos, as cópias de segurança são copiadas para as partilhas NFS representadas através de uma VM e o armazenamento do Azure.

- **Infraestrutura de cópia de segurança e restaurar a funcionalidade.** Também pode utilizar a cópia de segurança e restaurar a funcionalidade que fornece a infra-estrutura subjacente do SAP HANA no Azure (instâncias grandes). Esta opção atende a necessidade de cópias de segurança e restauros rápidos. O restante desta seção aborda a funcionalidade de cópia de segurança e restauro é oferecida com instâncias grandes do HANA. Esta secção também inclui a relação que tenham de cópia de segurança e restauro para o desastre funcionalidade de recuperação oferecidos pelo instâncias grandes do HANA.

> [!NOTE]
>   A tecnologia de instantâneo que é usada pela infra-estrutura subjacente de instâncias grandes do HANA tem uma dependência no instantâneos do SAP HANA. Neste momento, os instantâneos de SAP HANA não funcionam em conjunto com vários inquilinos de contentores de multi-inquilino da base de dados do SAP HANA. Se apenas um inquilino é implementado, os instantâneos de SAP HANA funcionam e pode usar esse método.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Utilizar instantâneos de armazenamento do SAP HANA no Azure (instâncias grandes)

A infraestrutura de armazenamento subjacente do SAP HANA no Azure (instâncias grandes) suporta instantâneos de armazenamento de volumes. Cópia de segurança e restauro de volumes é suportada com as seguintes considerações:

- Em vez de cópias de segurança completa da base de dados, são tirados instantâneos de volume de armazenamento com frequência.
- Quando um instantâneo é acionado através de /hana/data e /hana/shared, que inclui /usr/sap, volumes, a tecnologia de instantâneo inicia um instantâneo antes do SAP HANA é executado o instantâneo de armazenamento. Este instantâneo do SAP HANA é o ponto de instalação para restaurações de eventual registo após a recuperação do instantâneo de armazenamento. Para um instantâneo do HANA ter êxito, terá de uma instância ativa do HANA. Num cenário HSR, um instantâneo de armazenamento não é suportado num nó secundário atual em que não é possível efetuar um instantâneo do HANA.
- Depois do instantâneo de armazenamento é executado com êxito, o instantâneo do SAP HANA é eliminado.
- Backups de log de transação são executadas com frequência e armazenados no /hana/logbackups volume ou no Azure. Pode disparar o volume de /hana/logbackups que contém os backups de log de transação para tirar um instantâneo em separado. Nesse caso, não precisa de executar um instantâneo do HANA.
- Se tem de restaurar uma base de dados para um determinado ponto no tempo, para uma interrupção de produção, solicitar esse suporte do Microsoft Azure ou o SAP HANA no Azure restauro para um determinado instantâneo de armazenamento. Um exemplo é uma restauração planeada de um sistema de proteção de segurança para o estado original.
- O instantâneo do SAP HANA que está incluído no instantâneo de armazenamento é um ponto de deslocamento para a aplicação de backups de log de transação que foi executada e eram armazenados após o instantâneo de armazenamento foi criado.
- Estas cópias de segurança do registo de transação serão direcionadas para restaurar a base de dados para um determinado ponto no tempo.

Pode executar os instantâneos de armazenamento que três classes de volumes de destino:

- Um instantâneo combinado sobre/hana/dados e/hana/partilhada, que inclui /usr/sap. Este instantâneo requer a criação de um instantâneo do SAP HANA como preparação para o instantâneo de armazenamento. O instantâneo do SAP HANA garante que a base de dados está num estado consistente de um ponto de vista do armazenamento. Para o processo de restauração, que é um ponto de configurar no.
- Um instantâneo separado sobre/hana/logbackups.
- Uma partição de sistema operativo.

Para obter os scripts de instantâneo e a documentação mais recente, consulte [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Ao baixar o pacote de script de instantâneo do [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0), receberá três arquivos. Um dos ficheiros está documentado num PDF para a funcionalidade fornecida. Depois de transferir o conjunto de ferramentas, siga as instruções em "obter as ferramentas de instantâneo".

## <a name="storage-snapshot-considerations"></a>Considerações de instantâneo de armazenamento

>[!NOTE]
>Instantâneos de armazenamento consumam espaço de armazenamento que está alocado para as unidades de instância grande do HANA. Considere os seguintes aspetos de agendamento de instantâneos de armazenamento e o número de instantâneos de armazenamento para manter. 

A mecânica específica de instantâneos de armazenamento para o SAP HANA no Azure (instâncias grandes) incluem:

- Um instantâneo de armazenamento específico no ponto no tempo quando é tirado consome armazenamento pouco.
- Como as alterações de conteúdo de dados e o conteúdo em ficheiros de alteração no volume de armazenamento de dados do SAP HANA, o instantâneo tem de armazenar o conteúdo original do bloco e as alterações de dados.
- Como resultado, o instantâneo de armazenamento aumenta de tamanho. Existe mais tempo de instantâneo, fica do quanto maior for o instantâneo de armazenamento.
- Mais alterações efetuadas para o volume de base de dados do SAP HANA ao longo do tempo de vida de um instantâneo de armazenamento, o maior o consumo de espaço do instantâneo de armazenamento.

SAP HANA no Azure (instâncias grandes) é fornecido com tamanhos de volume fixo para os volumes de dados e de registo do SAP HANA. Execução de instantâneos desses volumes come no espaço de volume. Tem de:

- Determine quando deve agendar instantâneos de armazenamento.
- Monitorize o consumo de espaço dos volumes de armazenamento. 
- Gerir o número de instantâneos que armazena. 

Pode desativar os instantâneos de armazenamento quando importar massas de dados ou efetuar outras alterações significativas na base de dados do HANA. 


As secções seguintes fornecem informações para efetuar estes instantâneos e incluem recomendações gerais:

- Embora o hardware possa suportar 255 instantâneos por volume, quer manter bem abaixo deste número. A recomendação é 250 ou menos.
- Antes de executar os instantâneos de armazenamento, monitorizar e manter o controle de espaço livre.
- Reduza o número de instantâneos de armazenamento com base no espaço livre. Pode reduzir o número de instantâneos que mantenha ou pode expandir os volumes. É possível pedir armazenamento adicional em unidades de 1 terabyte.
- Durante as atividades, como mover dados para SAP HANA com ferramentas de migração de plataforma para SAP (R3load) ou o restauro de bases de dados do SAP HANA de cópias de segurança, desative os instantâneos de armazenamento no /hana/data volume. 
- Durante a maior reorganizations das tabelas de SAP HANA, evite instantâneos de armazenamento se possível.
- Os instantâneos de armazenamento são um pré-requisito para tirar partido do desastre capacidades de recuperação do SAP HANA no Azure (instâncias grandes).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Pré-requisitos para utilizar instantâneos de armazenamento de self-service

Para certificar-se de que o script de instantâneos é executado com êxito, certifique-se de que o Perl está instalado no sistema operativo Linux no servidor de instâncias grandes do HANA. Perl vem pré-instalada em sua unidade de instância grande do HANA. Para verificar a versão do Perl, utilize o seguinte comando:

`perl -v`

![A chave pública é copiada ao executar este comando](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Configurar instantâneos de armazenamento

Para configurar a instantâneos de armazenamento com instâncias grandes do HANA, siga estes passos.
1. Certifique-se de que o Perl está instalado no sistema operativo Linux no servidor de instâncias grandes do HANA.
1. Modificar o/etc/ssh/ssh\_config para adicionar a linha _MACs hmac-sha1_.
1. Crie uma conta de utilizador de cópia de segurança do SAP HANA no nó principal para cada instância do SAP HANA que executar, se aplicável.
1. Instale o cliente do SAP HANA HDB em todos os servidores de instâncias grandes do SAP HANA.
1. No primeiro servidor SAP HANA nas instâncias grandes de cada região, crie uma chave pública para aceder a infraestrutura de armazenamento subjacente, que controla a criação do instantâneo.
1. Copiar os scripts e o ficheiro de configuração da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) para a localização dos **hdbsql** na instalação do SAP HANA.
1. Modificar a *HANABackupDetails.txt* ficheiro conforme necessário para as especificações de cliente adequado.

Obtenha as mais recentes scripts de instantâneo e documentação de [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Para obter os passos listados anteriormente, consulte [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

### <a name="consideration-for-mcod-scenarios"></a>Consideração para cenários MCOD
Se executar uma [cenário MCOD](https://launchpad.support.sap.com/#/notes/1681092) com várias instâncias do SAP HANA numa só unidade de instância grande do HANA, tem volumes de armazenamento separada aprovisionados para cada uma das instâncias do SAP HANA. Para obter mais informações sobre MDC e outras considerações, consulte "Aspetos importantes a lembrar-se" no [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Passo 1: Instalar o cliente do SAP HANA HDB

O sistema operativo Linux instalado no SAP HANA no Azure (instâncias grandes) inclui as pastas e os scripts necessários para executar os instantâneos de armazenamento do SAP HANA para cópia de segurança e desastre fins de recuperação. A verificação para versões mais recentes na [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). A versão de lançamento mais recente dos scripts é 4.0. Scripts de diferentes podem ter diferentes versões secundárias dentro da mesma versão principal.

É da responsabilidade do cliente para instalar o cliente de SAP HANA HDB das unidades de instância grande do HANA durante a instalação de SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Passo 2: Alterar o/etc/ssh/ssh\_config

Este passo é descrito em "Ativar comunicação com o armazenamento" [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


### <a name="step-3-create-a-public-key"></a>Passo 3: Criar uma chave pública

Para ativar o acesso para as interfaces de instantâneo de armazenamento do seu inquilino de instância grande do HANA, estabeleça um procedimento de início de sessão por meio de uma chave pública. 

No SAP HANA primeiro no servidor do Azure (instâncias grandes) no seu inquilino, crie uma chave pública para aceder a infraestrutura de armazenamento. Com uma chave pública, uma palavra-passe não é necessário para iniciar sessão para as interfaces de instantâneo de armazenamento. Também não precisa de manter credenciais de palavra-passe com uma chave pública. 

Para gerar uma chave pública, consulte "Ativar comunicação com o armazenamento de" na [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


### <a name="step-4-create-an-sap-hana-user-account"></a>Passo 4: Criar uma conta de utilizador do SAP HANA

Para iniciar a criação de instantâneos de SAP HANA, crie uma conta de utilizador no SAP HANA que podem utilizar os scripts de instantâneo de armazenamento. Crie uma conta de utilizador do SAP HANA no SAP HANA Studio para esta finalidade. O utilizador tem de ser criado sob o SYSTEMDB e *não* sob a base de dados do SID para MDC. No ambiente de contentor único, o utilizador é criado na base de dados do inquilino. Esta conta tem de ter **cópia de segurança do administrador** e **catálogo leitura** privilégios. 

Para configurar e utilizar uma conta de utilizador, consulte "Ativar comunicação com o SAP HANA" em [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Passo 5: Autorizar a conta de utilizador do SAP HANA

Neste passo, irá autorizar a conta de utilizador do SAP HANA que criou para que os scripts não tem de submeter as palavras-passe em tempo de execução. O comando de SAP HANA `hdbuserstore` permite a criação de uma chave de utilizador do SAP HANA. A chave é armazenada num ou mais nós de SAP HANA. A chave de utilizador permite ao utilizador aceder SAP HANA, sem ter de gerir palavras-passe de dentro do processo de criação de scripts. O processo de criação de scripts é abordado neste artigo.

>[!IMPORTANT]
>Execute estes comandos de configuração com o mesmo contexto de utilizador que os comandos de instantâneo são executados no. Caso contrário, os comandos de instantâneo não funcionarão corretamente.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Passo 6: Obter os scripts de instantâneo, os instantâneos de configurar e testar a configuração e a conectividade

Baixe a versão mais recente dos scripts da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Alterar a forma como os scripts estão instalados com a versão 4.0 dos scripts. Para obter mais informações, consulte "Ativar comunicação com o SAP HANA" no [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Para obter a seqüência exata dos comandos, consulte "Instalação fácil de ferramentas de instantâneo (predefinição)" no [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Recomendamos a utilização da instalação predefinida. 

Para atualizar da versão 3.x 4.0, consulte "Atualizar uma instalação existente" no [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Para desinstalar o conjunto de 4.0 ferramentas, consulte "Desinstalação das ferramentas do instantâneo" na [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Não se esqueça de executar os passos descritos em "Concluir a configuração das ferramentas de instantâneo" na [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

A finalidade das diferentes scripts e arquivos à medida que acabou de instalar é descrita em "Quais são essas ferramentas de instantâneo?" na [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Antes de configurar as ferramentas de instantâneo, certifique-se de que configurou também HANA localizações de cópia de segurança e as definições corretamente. Para obter mais informações, consulte "Configuração do SAP HANA" no [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

A configuração do conjunto de ferramentas de instantâneo é descrita no "Arquivo de configuração - HANABackupCustomerDetails.txt" na [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="test-connectivity-with-sap-hana"></a>Testar a conectividade com o SAP HANA

Depois de colocar todos os dados de configuração para o *HANABackupCustomerDetails.txt* de ficheiros, verifique se as configurações estão corretas para os dados da instância HANA. Utilize o script `testHANAConnection`, que é independente de uma configuração de aumento vertical ou horizontal do SAP HANA.

Para obter mais informações, consulte "Verifique a conectividade com o SAP HANA - testHANAConnection" no [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="test-storage-connectivity"></a>Testar a conectividade de armazenamento

A próxima etapa de teste é verificar a conectividade para o armazenamento com base nos dados que colocou na *HANABackupCustomerDetails.txt* ficheiro de configuração. Em seguida, execute um instantâneo de teste. Antes de executar o `azure_hana_backup` de comando, tem de executar este teste. Para a sequência de comandos para este teste, consulte "Verifique a conectividade com o armazenamento - testStorageSnapshotConnection" "na [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Após um êxito início de sessão para as interfaces de máquina virtual de armazenamento, o script continua na fase 2 e cria um instantâneo de teste. O resultado é mostrado aqui para uma configuração de escalamento horizontal de três nós do SAP HANA.

Se o instantâneo de teste for executado com êxito com o script, pode agendar os instantâneos de armazenamento real. Se não for bem-sucedida, investigue os problemas antes de avançar. O instantâneo de teste deve permanecer por perto, até que os primeiros instantâneos reais são feitos.


### <a name="step-7-perform-snapshots"></a>Passo 7: Executar instantâneos

Quando os passos de preparação estiverem concluídos, pode começar a configurar e agendar os instantâneos de armazenamento real. O script ser agendada funciona com configurações de aumento vertical e horizontal do SAP HANA. Para execução do script de segurança regular e periódica, agende o script usando o utilitário de cron. 

Para a sintaxe de comando exatas e a funcionalidade, consulte "Executar instantâneo cópia de segurança - azure_hana_backup" no [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

Quando o script `azure_hana_backup` é executado, ele cria o armazenamento de instantâneos nas seguintes três fases:

1. Ele é executado um instantâneo do SAP HANA.
1. Ele é executado um instantâneo de armazenamento.
1. Remove o instantâneo do SAP HANA que foi criado antes do instantâneo de armazenamento foi executado.

Para executar o script, chamá-lo a partir da pasta executável do HDB ao qual foi copiado. 

O período de retenção é administrado com o número de instantâneos que são enviados como um parâmetro ao executar o script. A quantidade de tempo que é abrangido pelos instantâneos de armazenamento é uma função do período de execução e do número de instantâneos submetido como um parâmetro quando o script é executado. 

Se o número de instantâneos são mantidos exceder o número que são nomeados como um parâmetro na chamada do script, o instantâneo de armazenamento mais antigo da etiqueta do mesmo é eliminado antes da execução de um novo instantâneo. O número é conceder à medida que o último parâmetro da chamada é o número que pode utilizar para controlar o número de instantâneos são mantidos. Com este número, também pode controlar, indiretamente, o espaço em disco que é utilizado para instantâneos. 


## <a name="snapshot-strategies"></a>Estratégias de instantâneo
A frequência de instantâneos para os diferentes tipos depende se utilizar a funcionalidade de recuperação de desastres de instância grande do HANA. Esta funcionalidade baseia-se em instantâneos de armazenamento, que podem exigir recomendações especiais para os períodos de frequência e a execução de instantâneos de armazenamento. 

As considerações e recomendações que se seguem, a suposição é que possa *não* utilizar a funcionalidade de recuperação após desastre que oferece instâncias grandes do HANA. Em vez disso, utilize os instantâneos de armazenamento para ter cópias de segurança e ser capaz de fornecer a recuperação de ponto no tempo nos últimos 30 dias. Devido às limitações do número de instantâneos e espaço, considere os seguintes requisitos:

- O tempo de recuperação para a recuperação de ponto no tempo.
- O espaço utilizado.
- O ponto de recuperação e objetivos de tempo de recuperação para potencial recuperação de desastres.
- A execução eventual de cópias de segurança de base de dados completo HANA em relação a discos. Sempre que uma cópia de segurança da base de dados completo em relação a discos ou o **backint** interface é executada, a execução de instantâneos de armazenamento falha. Se planeja executar cópias de segurança de base de dados completo em cima de instantâneos de armazenamento, certifique-se de que a execução de instantâneos de armazenamento é desativada durante este período.
- O número de instantâneos por volume, o que está limitado a 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Se não usar a funcionalidade de recuperação após desastre de instâncias grandes do HANA, o período de instantâneo é menos freqüente. Nesses casos, realize os instantâneos combinados no /hana/data e /hana/shared, que inclui /usr/sap, em períodos de 12 ou 24 horas. Mantenha os instantâneos durante um mês. O mesmo acontece com os instantâneos do volume de cópia de segurança de registo. A execução de backups de log de transação de SAP HANA com o volume de cópia de segurança do registo ocorre em 5 minutos para períodos de 15 minutos.

Instantâneos de armazenamento agendada são melhor efetuados com cron. Utilize o mesmo script para todas as cópias de segurança e necessidades de recuperação após desastre. Modificar o script entradas de acordo com as várias solicitado tempos de backup. Estes instantâneos são todos agendados forma diferente de cron dependendo do tempo de execução. Pode ser por hora, cada 12 horas, diariamente, ou semanal. 

O exemplo seguinte mostra uma agenda cron no /etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
No exemplo anterior, um instantâneo de combinado por hora aborda os volumes que contêm o /hana/data e /hana/shared/SID, que inclui /usr/sap, localizações. Utilize este tipo de instantâneo para uma recuperação mais rápida do ponto anterior no tempo dentro os últimos dois dias. Também é um instantâneo nesses volumes diário. Por isso, têm dois dias de cobertura pelo instantâneos por hora mais quatro semanas de cobertura por instantâneos diários. O volume de cópia de segurança de registo de transação também é uma cópia de segurança diária. Estas cópias de segurança são mantidas durante quatro semanas. 

Como pode ver na terceira linha da crontab, a cópia de segurança do registo de transação de HANA está agendada para ser executado a cada 5 minutos. As horas de início das tarefas de cron diferentes com instantâneos de armazenamento são escalonadas. Dessa forma, os instantâneos não são executados ao mesmo tempo num certo ponto no tempo. 

O exemplo seguinte, vai realizar um instantâneo combinado que abrange os volumes que contêm o /hana/data e /hana/shared/SID, que inclui /usr/sap, localizações numa base horária. Mantenha esses instantâneos para dois dias. Os instantâneos de volumes de cópia de segurança de registo de transação executam numa base de 5 minutos e são mantidos durante quatro horas. Como antes, a cópia de segurança do arquivo de log de transações de HANA é agendada para ser executada a cada 5 minutos. 

O instantâneo do volume de cópia de segurança do registo de transação é realizado com um atraso de 2 minutos, depois da cópia de segurança do registo de transação foi iniciada. Em circunstâncias normais, o backup de log de transação do SAP HANA termina nesses dois minutos. Como antes, o volume que contém o arranque LUN uma vez por dia é apoiado por um instantâneo de armazenamento e é mantido por quatro semanas.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

O gráfico seguinte ilustra as sequências de exemplo anterior. O arranque LUN está excluído.

![Relação entre as cópias de segurança e instantâneos](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA executa escritas regulares com o volume de /hana/log documentar as alterações confirmadas para a base de dados. Regularmente, o SAP HANA escreve um ponto de reposição no volume de /hana/data. Conforme especificado no crontab, uma cópia de segurança do registo de transação do SAP HANA é executado a cada 5 minutos. 

Também verá que um instantâneo do SAP HANA é executada a cada hora, como resultado de um instantâneo sobre os volumes /hana/data e /hana/shared/SID de armazenamento combinado a acionar. Depois do instantâneo do HANA for bem-sucedida, o armazenamento combinado instantâneo é executado. As instruções na crontab, o instantâneo de armazenamento no /hana/logbackup volume executa cada 5 minutos, cerca de 2 minutos após a cópia de segurança do registo de transações do HANA.

> 

>[!IMPORTANT]
> A utilização de instantâneos de armazenamento para cópias de segurança do SAP HANA é valiosa apenas quando os instantâneos são realizados em conjunto com backups de log de transação do SAP HANA. Estas cópias de segurança do registo de transação tem de abranger os períodos de tempo entre os instantâneos de armazenamento. 

Se tiver definido um compromisso para os utilizadores de uma recuperação de ponto no tempo de 30 dias, terá de:

- Aceda a um instantâneo de armazenamento combinado ao longo do /hana/data e /hana/shared/SID que é 30 dias de antiguidade, em casos extremos. 
- Ter backups de log de transação contíguo que abrangem o tempo entre qualquer um dos instantâneos de armazenamento combinado. Então, o instantâneo mais antigo do volume de cópia de segurança do registo de transação tem de ser 30 dias de antiguidade. Isso não é o caso, se copiar os backups de log de transação para outra partilha NFS que está localizada no armazenamento do Azure. Nesse caso, poderia obter backups de log de transação antigos do que a partilha NFS.

Para tirar partido dos instantâneos de armazenamento e a replicação de armazenamento eventual dos backups de log de transação, altere a localização a que o SAP HANA escreve os backups de log de transação. Pode fazer esta alteração no HANA Studio. 

Embora o SAP HANA faz o backup de segmentos de registo completo automaticamente, especifique um intervalo de cópia de segurança de registo para ser determinística. Isso é especialmente verdadeiro quando utiliza a opção de recuperação após desastre, porque geralmente deseja executar backups de log com um período determinístico. No caso seguinte, 15 minutos é definido como o intervalo de cópia de segurança do registo.

![Registos de agendar cópia de segurança do SAP HANA no SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Também pode escolher as cópias de segurança que são mais freqüentes que a cada 15 minutos. Uma definição mais freqüente, muitas vezes, é utilizada em conjunto com a funcionalidade de recuperação após desastre de instâncias grandes do HANA. Alguns clientes efetuar cópias de segurança de registo de transações a cada 5 minutos.

Se a base de dados nunca foi feito o backup, a etapa final é efetuar uma cópia de segurança da base de dados baseados em ficheiros para criar uma única entrada de cópia de segurança que tem de existir dentro do catálogo de cópia de segurança. Caso contrário, o SAP HANA não pode iniciar as cópias de segurança de registo especificado.

![Fazer uma cópia de segurança baseados em ficheiros para criar uma única entrada de cópia de segurança](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Depois de executam a sua primeira instantâneos de armazenamento com êxito, elimine o instantâneo de teste que tenha sido executada no passo 6. Para obter mais informações, consulte "Remover teste instantâneos - removeTestStorageSnapshot" no [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitorizar o número e tamanho de instantâneos no volume de disco

Num volume de armazenamento específico, pode monitorizar o número de instantâneos e o consumo de armazenamento desses instantâneos. O `ls` comando não mostra os diretórios de instantâneo ou ficheiros. O comando de SO Linux `du` mostra detalhes sobre os instantâneos de armazenamento, uma vez que estão armazenados nos volumes mesmo. Utilize o comando com as seguintes opções:

- `du –sh .snapshot`: Esta opção fornece um total de todos os instantâneos dentro do diretório de instantâneo.
- `du –sh --max-depth=1`: Esta opção apresenta uma lista de todos os instantâneos são salvas no **.snapshot** pasta e o tamanho de cada instantâneo.
- `du –hc`: Esta opção fornece o tamanho total utilizado por todos os instantâneos.

Utilize estes comandos para se certificar de que os instantâneos que são executados e armazenados não consumam todas as o armazenamento nos volumes.

>[!NOTE]
>Os instantâneos da inicialização LUN não são visíveis com os comandos anteriores.

### <a name="get-details-of-snapshots"></a>Obter os detalhes de instantâneos
Para obter mais detalhes sobre os instantâneos, utilize o script `azure_hana_snapshot_details`. Pode executar este script em ambos os locais, se houver um servidor ativo na localização de recuperação após desastre. O script fornece o seguinte resultado, dividido por cada volume que contém instantâneos: 
   * O tamanho de instantâneos total num volume
   * Os seguintes detalhes de cada instantâneo desse volume: 
      - Nome do instantâneo 
      - Hora de criação 
      - Tamanho do instantâneo
      - Frequência do instantâneo
      - ID de cópia de segurança do HANA associado a esse instantâneo, se forem relevantes

Para a sintaxe do comando e saídas, consulte "Listar instantâneos - azure_hana_snapshot_details" no [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Reduzir o número de instantâneos num servidor

Como explicado anteriormente, pode reduzir o número de determinados rótulos de instantâneos que armazena. Os dois últimos parâmetros do comando para iniciar um instantâneo são a etiqueta e o número de instantâneos que pretenda manter.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

No exemplo anterior, é a etiqueta de instantâneo **dailyhana**. É o número de instantâneos com esta etiqueta de ser mantidos **28**. Como responde ao consumo de espaço em disco, poderá reduzir o número de instantâneos armazenados. Uma forma fácil de reduzir o número de instantâneos para 15, por exemplo, está a executar o script com o último parâmetro definido como **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Se executar o script com esta definição, o número de instantâneos, que inclui o novo instantâneo de armazenamento, é 15. Os instantâneos de mais recentes 15 são mantidos e os 15 instantâneos mais antigos são eliminados.

 >[!NOTE]
 > Este script reduz o número de instantâneos apenas se houver mais de um horas depois de instantâneos. O script não elimina os instantâneos são menos de um horas depois. Estas restrições relacionadas com a funcionalidade de recuperação após desastre opcional fornecida.

Se já não pretende manter um conjunto de instantâneos com o prefixo de cópia de segurança **dailyhana** nos exemplos de sintaxe, execute o script com **0** como o número de retenção. Todos os instantâneos que correspondem a essa etiqueta, em seguida, são removidos. Remover todos os instantâneos pode afetar as capacidades da funcionalidade de recuperação após desastre de instâncias grandes do HANA.

Uma segunda opção para eliminar instantâneos específicos é utilizar o script `azure_hana_snapshot_delete`. Este script foi desenvolvido para eliminar um instantâneo ou um conjunto de instantâneos, com o ID de cópia de segurança do HANA como encontrada no HANA Studio ou o nome de instantâneo em si. Atualmente, o ID da cópia de segurança só está associado ao instantâneos criados para o **hana** tipo de instantâneo. As cópias de segurança do tipo de instantâneo **logs** e **arranque** não efetuam uma SAP HANA de instantâneos, portanto, não há nenhum ID de cópia de segurança a serem encontradas para os instantâneos. Se o nome do instantâneo é introduzido, ele procura por todos os instantâneos em volumes diferentes que corresponde ao nome do instantâneo introduzido. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Para obter mais informações sobre o script, consulte "Eliminar um instantâneo - azure_hana_snapshot_delete" no [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Execute o script como usuário **raiz**.

>[!IMPORTANT]
>Se houver dados existentes apenas no instantâneo que pretende eliminar, depois de eliminar o instantâneo, que os dados são perdidos para sempre.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Restauro ao nível do ficheiro a partir de um instantâneo de armazenamento

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Para os tipos de instantâneo **hana** e **registos**, pode acessar os instantâneos diretamente nos volumes do **.snapshot** diretório. Há um subdiretório para cada um dos instantâneos. Copie cada ficheiro no Estado em que estava no ponto do instantâneo desse subdiretório à estrutura de diretório real. 

Na versão atual do script, não há *nenhuma* restaurar o script fornecido para o restauro de instantâneo como Self-Service. Restauro de instantâneo pode ser executado como parte dos scripts de recuperação após desastre de self-service no site de recuperação após desastre durante a ativação pós-falha. Para restaurar um instantâneo pretendido dos instantâneos disponíveis existentes, tem de contactar a equipa de operações do Microsoft ao abrir um pedido de serviço.

>[!NOTE]
>Arquivo único restore não funciona para instantâneos da inicialização LUN independente do tipo de unidades de instância grande do HANA. O **.snapshot** diretório não está exposto no LUN de arranque. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Recuperar para o instantâneo mais recente do HANA

Num cenário de produção para baixo, o processo de recuperação a partir de um instantâneo de armazenamento pode ser iniciado como um incidente de cliente com suporte do Microsoft Azure. É uma questão de urgência alta se dados foi eliminados num sistema de produção e a única forma de recuperá-la restaurar a base de dados de produção.

Numa situação de diferente, um ponto anterior no tempo de recuperação poderá ser urgência baixa e planeadas dias com antecedência. Pode planear esta recuperação com o SAP HANA no Azure em vez de gerar um sinalizador de alta prioridade. Por exemplo, pode planear a atualização de SAP software ao aplicar um novo pacote de aprimoramento. Em seguida, terá de reverter para um instantâneo que representa o estado antes da atualização do pacote de aprimoramento.

Antes de enviar o pedido, terá de preparar. O SAP HANA na equipa do Azure, em seguida, pode manipular a solicitação e fornecer os volumes restaurados. Em seguida, restaurar a base de dados do HANA com base nos instantâneos.

Para as possibilidades para obter um instantâneo restaurado com o novo conjunto de ferramentas, consulte "Como restaurar um instantâneo" na [guia de recuperação Manual do SAP HANA no Azure a partir de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Para preparar o pedido, siga estes passos.

1. Decida qual instantâneo para restaurar. Apenas o volume de dados do hana é restaurado, a menos que instruir o contrário. 

1. Encerre a instância do HANA.

   ![Encerrar a instância HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Desmonte os volumes de dados em cada nó de base de dados do HANA. Se os volumes de dados ainda são instalados no sistema operativo, o restauro do instantâneo de falha.

   ![Desmontar os volumes de dados em cada nó de base de dados HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Abra um pedido de suporte do Azure e inclui instruções sobre a restauração de um instantâneo específico:

   - Durante o restauro: SAP HANA no serviço do Azure pode pedir-lhe para assistir uma chamada de conferência para coordenar, verifique e confirme que o instantâneo de armazenamento correta é restaurado. 

   - Após o restauro: SAP HANA no serviço do Azure notifica-o quando o instantâneo de armazenamento é restaurado.

1. Depois do processo de restauro estiver concluído, voltar a montar os volumes de dados.

   ![Voltar a montar os volumes de dados](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Outra possibilidade para obter, por exemplo, ficheiros de dados do SAP HANA recuperados de um instantâneo de armazenamento, está documentada no passo 7 [guia de recuperação Manual do SAP HANA no Azure a partir de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Para restaurar a partir de uma cópia de segurança do instantâneo, veja [guia de recuperação Manual do SAP HANA no Azure a partir de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf). 

>[!Note]
>Se o instantâneo foi restaurado por operações da Microsoft, não precisa de passo 7.


### <a name="recover-to-another-point-in-time"></a>Recuperar para um outro ponto no tempo
Para restaurar para um determinado ponto no tempo, consulte "Recuperar a base de dados para o seguinte ponto no tempo" em [guia de recuperação Manual do SAP HANA no Azure a partir de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf). 


## <a name="next-steps"></a>Passos Seguintes
- Ver [princípios de recuperação após desastre e a preparação](hana-concept-preparation.md).
