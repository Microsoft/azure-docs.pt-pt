---
title: Hana backup e restaurar em SAP HANA em Azure (Grandes Instâncias) / Microsoft Docs
description: Como executar backup HANA e restaurar em SAP HANA em Azure (Grandes Instâncias)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4384d29811d29f06422802abba5d3eb1ea5737e9
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "72430087"
---
# <a name="backup-and-restore"></a>Cópia de segurança e restauro

>[!IMPORTANT]
>Este artigo não substitui a documentação da administração SAP HANA ou as Notas SAP. Esperamos que tenha uma compreensão sólida e experiência na administração e operações da SAP HANA, especialmente para backup, restauro, alta disponibilidade e recuperação de desastres. Neste artigo, são mostradas imagens do Estúdio SAP HANA. Conteúdo, estrutura e natureza dos ecrãs das ferramentas de administração SAP e das próprias ferramentas podem mudar a partir da versão SAP HANA para lançar.

É importante que exerça os passos e processos tomados no seu ambiente e com as suas versões e lançamentos HANA. Alguns processos descritos neste artigo são simplificados para uma melhor compreensão geral. Não devem ser usados como passos detalhados para uma eventual operação de manuais. Se pretender criar manualde operação para as suas configurações, testar e exercitar os seus processos e documentar os processos relacionados com as suas configurações específicas. 

Um dos aspetos mais importantes das bases de dados operacionais é protegê-las de eventos catastróficos. A causa destes eventos pode ser qualquer coisa, desde desastres naturais a erros simples do utilizador.

O backup de uma base de dados, com a capacidade de restaurá-la a qualquer momento, como antes de alguém apagar dados críticos, permite a restauração a um estado o mais próximo possível da forma como estava antes da interrupção.

Devem ser realizados dois tipos de cópias de segurança para alcançar a capacidade de restauro:

- Backups de base de dados: Backups completos, incrementais ou diferenciais
- Backups de registo de transações

Além das cópias de segurança completas realizadas a nível de aplicação, pode efetuar cópias de segurança com instantâneos de armazenamento. Os instantâneos de armazenamento não substituem as cópias de segurança do registo de transações. As cópias de segurança do registo de transações continuam a ser importantes para restaurar a base de dados num determinado momento ou para esvaziar os registos de transações já comprometidas. Os instantâneos de armazenamento podem acelerar a recuperação fornecendo rapidamente uma imagem de avanço da base de dados. 

O SAP HANA on Azure (Grandes Instâncias) oferece duas opções de backup e restauro:

- **Faça-o por si mesmo (DIY).** Depois de se certificar de que existe espaço suficiente para o disco, execute a base de dados completa e faça cópias de segurança através de um dos seguintes métodos de backup do disco. Pode fazer o seu back back diretamente aos volumes ligados às unidades HANA Large Instance ou às ações da NFS que são configuradas numa máquina virtual Azure (VM). Neste último caso, os clientes criaram um Linux VM em Azure, anexam o Armazenamento Azure ao VM e partilham o armazenamento através de um servidor NFS configurado nesse VM. Se executar a cópia de segurança contra volumes que se ligam diretamente às unidades HANA Large Instance, copie as cópias de cópias de segurança para uma conta de armazenamento Azure. Faça isto depois de criar um VM Azure que exporta ações da NFS baseadas no Armazenamento Azure. Também pode utilizar um cofre azure backup ou armazenamento a frio Azure. 

   Outra opção é usar uma ferramenta de proteção de dados de terceiros para armazenar as cópias de segurança depois de serem copiadas para uma conta de armazenamento Azure. A opção de backup DIY também pode ser necessária para os dados que você precisa armazenar por períodos mais longos de tempo para finalidades de conformidade e auditoria. Em todos os casos, as cópias de backup são copiadas em ações da NFS representadas através de um VM e Armazenamento Azure.

- **Infraestrutura de backup e restaurar funcionalidade.** Também pode utilizar a funcionalidade de backup e restauro que a infraestrutura subjacente do SAP HANA no Azure (Grandes Instâncias) fornece. Esta opção satisfaz a necessidade de backups e restauros rápidos. O resto desta secção aborda a funcionalidade de backup e restauro que é oferecida com as Grandes Instâncias HANA. Esta secção também cobre a relação que o backup e o restauro têm com a funcionalidade de recuperação de desastres oferecida pela HANA Large Instances.

> [!NOTE]
>   A tecnologia instantânea que é usada pela infraestrutura subjacente de HANA Large Instances tem uma dependência de instantâneos SAP HANA. Neste ponto, as fotos do SAP HANA não funcionam em conjunto com vários inquilinos de contentores de base de dados multiarrendatários SAP HANA. Se apenas um inquilino for implantado, as fotos do SAP HANA funcionam e você pode usar este método.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Utilize instantâneos de armazenamento de SAP HANA em Azure (Grandes Instâncias)

A infraestrutura de armazenamento subjacente ao SAP HANA no Azure (Grandes Instâncias) suporta instantâneos de armazenamento de volumes. Tanto a cópia de segurança como a restauração de volumes são suportadas, com as seguintes considerações:

- Em vez de cópias de dados completas, as imagens de volume de armazenamento são tiradas frequentemente.
- Quando um instantâneo é acionado sobre /hana/data e /hana/shared, que inclui /usr/seiva, volumes, a tecnologia snapshot inicia um instantâneo SAP HANA antes de ser executado o instantâneo de armazenamento. Este instantâneo SAP HANA é o ponto de configuração para eventuais restaurações de log após a recuperação do instantâneo de armazenamento. Para que uma foto hana tenha sucesso, precisa de um caso HANA ativo. Num cenário de HSR, um instantâneo de armazenamento não é suportado num nó secundário atual onde não se pode realizar um instantâneo HANA.
- Após o instantâneo de armazenamento ser executado com sucesso, o instantâneo SAP HANA é eliminado.
- As cópias de segurança do registo de transações são tomadas frequentemente e armazenadas no volume /hana/logbackups ou em Azure. Pode ativar o volume de backups /hana/logbackups que contém as cópias de segurança do registo de transações para tirar uma fotografia separadamente. Nesse caso, não precisas de fazer uma fotografia da HANA.
- Se tiver de restaurar uma base de dados a um determinado ponto do tempo, para uma paragem de produção, solicite que o Microsoft Azure Support ou o SAP HANA no Azure restaure a um determinado instantâneo de armazenamento. Um exemplo é a restauração planeada de um sistema de caixa de areia para o seu estado original.
- O instantâneo SAP HANA que está incluído no instantâneo de armazenamento é um ponto de compensação para a aplicação de cópias de segurança de registo de transações que foram realizadas e foram armazenadas após a foto de armazenamento ter sido tirada.
- Estas cópias de segurança do registo de transações são tomadas para restaurar a base de dados de volta a um certo ponto do tempo.

Pode executar instantâneos de armazenamento que visam três classes de volumes:

- Um instantâneo combinado sobre /hana/data e /hana/shared, que inclui /usr/seiva. Este instantâneo requer a criação de um instantâneo SAP HANA como preparação para o instantâneo de armazenamento. O instantâneo SAP HANA garante que a base de dados está num estado consistente do ponto de vista do armazenamento. Para o processo de restauro, é um ponto para definir.
- Uma imagem separada sobre /hana/logbackups.
- Uma partição do sistema operativo.

Para obter os mais recentes scripts e documentação instantânea, consulte [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Ao descarregar o pacote de scripts snapshot do [GitHub,](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)obtém três ficheiros. Um dos ficheiros está documentado num PDF para a funcionalidade fornecida. Depois de descarregar o conjunto de ferramentas, siga as instruções em "Obter as ferramentas instantâneas".

## <a name="storage-snapshot-considerations"></a>Considerações de instantâneode armazenamento

>[!NOTE]
>Os instantâneos de armazenamento consomem espaço de armazenamento que é atribuído às unidades HANA Large Instance. Considere os seguintes aspetos de agendamento de instantâneos de armazenamento e quantos instantâneos de armazenamento devem guardar. 

As mecânicas específicas dos instantâneos de armazenamento para SAP HANA em Azure (Grandes Instâncias) incluem:

- Uma foto de armazenamento específica no momento em que é tomada consome pouco armazenamento.
- À medida que o conteúdo dos dados muda e o conteúdo nos ficheiros de dados do SAP HANA muda no volume de armazenamento, o instantâneo precisa de armazenar o conteúdo original do bloco e as alterações de dados.
- Como resultado, o instantâneo de armazenamento aumenta de tamanho. Quanto mais tempo o instantâneo existir, maior se torna o instantâneo de armazenamento.
- Quanto mais alterações forem feitas ao volume de base de dados SAP HANA ao longo da vida útil de um instantâneo de armazenamento, maior é o consumo de espaço do instantâneo de armazenamento.

O SAP HANA em Azure (Grandes Instâncias) vem com tamanhos de volume fixos para os dados e volumes de registo SAP HANA. Executar instantâneos desses volumes come no seu espaço de volume. Precisa:

- Determine quando agendar instantâneos de armazenamento.
- Monitorize o consumo espacial dos volumes de armazenamento. 
- Gerencie o número de instantâneos que armazena. 

Pode desativar as imagens de armazenamento quando importar massas de dados ou realizar outras alterações significativas na base de dados da HANA. 


As seguintes secções fornecem informações para a realização destes instantâneos e incluem recomendações gerais:

- Embora o hardware possa sustentar 255 instantâneos por volume, você quer ficar bem abaixo deste número. A recomendação é de 250 ou menos.
- Antes de realizar instantâneos de armazenamento, monitorize e mantenha o registo do espaço livre.
- Baixe o número de instantâneos de armazenamento com base no espaço livre. Pode baixar o número de instantâneos que guarda, ou pode estender os volumes. Pode encomendar armazenamento adicional em unidades de 1 terabyte.
- Durante atividades como a deslocação de dados para SAP HANA com ferramentas de migração da plataforma SAP (R3load) ou restaurar as bases de dados SAP HANA de cópias de segurança, desative instantâneos de armazenamento no volume /hana/data. 
- Durante as maiores reorganizações das tabelas SAP HANA, evite instantâneos de armazenamento, se possível.
- Os instantâneos de armazenamento são um pré-requisito para tirar partido das capacidades de recuperação de desastres da SAP HANA em Azure (Grandes Instâncias).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Pré-requisitos para a utilização de instantâneos de armazenamento de self-service

Para se certificar de que o script instantâneo funciona com sucesso, certifique-se de que o Perl está instalado no sistema operativo Linux no servidor HANA Large Instances. Perl vem pré-instalado na sua unidade HANA Large Instance. Para verificar a versão Perl, utilize o seguinte comando:

`perl -v`

![A chave pública é copiada com este comando](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Configurar instantâneos de armazenamento

Para configurar instantâneos de armazenamento com as grandes instâncias HANA, siga estes passos.
1. Certifique-se de que o Perl está instalado no sistema operativo Linux no servidor HANA Large Instances.
1. Modificar o config /etc/ssh/ssh\_para adicionar a linha _MACs hmac-sha1_.
1. Crie uma conta de utilizador de reserva SAP HANA no nó principal para cada instância SAP HANA que executa, se aplicável.
1. Instale o cliente SAP HANA HDB em todos os servidores SAP HANA Large Instances.
1. No primeiro servidor SAP HANA Large Instances de cada região, crie uma chave pública para aceder à infraestrutura de armazenamento subjacente que controla a criação de instantâneos.
1. Copie os scripts e o ficheiro de configuração do [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) para a localização do **hdbsql** na instalação SAP HANA.
1. Modifique o ficheiro *HANABackupDetails.txt* conforme necessário para as especificações apropriadas do cliente.

Obtenha os mais recentes scripts instantâneos e documentação do [GitHub.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) Para os passos anteriormente listados, consulte as ferramentas snapshot da [Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

### <a name="consideration-for-mcod-scenarios"></a>Consideração para cenários MCOD
Se executar um [cenário MCOD](https://launchpad.support.sap.com/#/notes/1681092) com múltiplas instâncias SAP HANA numa unidade HANA Large Instance, tem volumes de armazenamento separados previstos para cada uma das instâncias SAP HANA. Para obter mais informações sobre o MDC e outras considerações, consulte "Coisas importantes a lembrar" nas [ferramentas instantâneas da Microsoft para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Passo 1: Instalar o cliente SAP HANA HDB

O sistema operativo Linux instalado no SAP HANA no Azure (Grandes Instâncias) inclui as pastas e scripts necessários para executar instantâneos de armazenamento SAP HANA para fins de backup e recuperação de desastres. Verifique se há lançamentos mais recentes no [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). 

É da sua responsabilidade instalar o cliente SAP HANA HDB nas unidades HANA Large Instance enquanto instala o SAP HANA.

### <a name="step-2-change-the-etcsshssh_config"></a>Passo 2: Alterar o config /etc/ssh/ssh\_

Este passo é descrito em "Enable communication with storage" nas [ferramentas instantâneas da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-3-create-a-public-key"></a>Passo 3: Criar uma chave pública

Para permitir o acesso às interfaces instantâneas de armazenamento do seu inquilino HANA Large Instance, estabeleça um procedimento de iniciação através de uma chave pública. 

No primeiro servidor SAP HANA no Azure (Grandes Instâncias) no seu inquilino, crie uma chave pública para aceder à infraestrutura de armazenamento. Com uma chave pública, não é necessária uma senha para iniciar sessão nas interfaces de instantâneo de armazenamento. Também não precisa de manter credenciais de senha com uma chave pública. 

Para gerar uma chave pública, consulte "Enable communication with storage" nas [ferramentas instantâneas da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-4-create-an-sap-hana-user-account"></a>Passo 4: Criar uma conta de utilizador SAP HANA

Para iniciar a criação de instantâneos SAP HANA, crie uma conta de utilizador no SAP HANA que os scripts instantâneos de armazenamento possam usar. Crie uma conta de utilizador SAP HANA dentro do Estúdio SAP HANA para este fim. O utilizador deve ser criado sob o SYSTEMDB e *não* sob a base de dados SID para MDC. No ambiente de contentor único, o utilizador é criado na base de dados dos inquilinos. Esta conta deve ter privilégios **de Backup Admin** e **Catalog Read.** 

Para configurar e utilizar uma conta de utilizador, consulte "Ativar a comunicação com o SAP HANA" no [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Passo 5: Autorizar a conta de utilizador SAP HANA

Neste passo, autoriza a conta de utilizador SAP HANA que criou para que os scripts não precisem de enviar senhas no prazo de execução. O comando `hdbuserstore` SAP HANA permite a criação de uma chave de utilizador SAP HANA. A chave é armazenada em um ou mais nós SAP HANA. A chave do utilizador permite ao utilizador aceder ao SAP HANA sem ter de gerir as palavras-passe a partir do processo de script. O processo de escrita é discutido mais tarde neste artigo.

>[!IMPORTANT]
>Executar estes comandos de configuração com o mesmo contexto de utilizador em que os comandos instantâneos são executados. Caso contrário, os comandos instantâneos não funcionarão corretamente.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Passo 6: Obtenha os scripts instantâneos, configure os instantâneos e teste a configuração e conectividade

Descarregue a versão mais recente dos scripts do [GitHub.](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1) A forma como os scripts são instalados alterou-se com o lançamento 4.1 dos scripts. Para mais informações, consulte "Enable communication with SAP HANA" nas [ferramentas instantâneas da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Para obter a sequência exata de comandos, consulte "Fácil instalação de ferramentas instantâneas (predefinido)" nas [ferramentas instantâneas da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Recomendamos a utilização da instalação predefinida. 

Para atualizar da versão 3.x para 4.1, consulte "Atualize uma instalação existente" nas [ferramentas instantâneas da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Para desinstalar o conjunto de ferramentas 4.1, consulte "Desinstalação das ferramentas instantâneas" nas [ferramentas instantâneas da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Não se esqueça de executar os passos descritos em "Configuração completa de ferramentas instantâneas" nas [ferramentas instantâneas da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

O propósito dos diferentes scripts e ficheiros à medida que foram instalados é descrito em "O que são estas ferramentas instantâneas?" em [ferramentas instantâneas da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Antes de configurar as ferramentas instantâneas, certifique-se de que também configura corretamente as localizações e configurações de backup HANA. Para mais informações, consulte "SAP HANA Configuration" nas [ferramentas instantâneas da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

A configuração do conjunto de ferramentas instantâneas é descrita em "Config file - HANABackupCustomerDetails.txt" nas [ferramentas instantâneas da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-connectivity-with-sap-hana"></a>Testar conectividade com SAP HANA

Depois de colocar todos os dados de configuração no ficheiro *HANABackupCustomerDetails.txt,* verifique se as configurações estão corretas para os dados da instância HANA. Utilize o `testHANAConnection`script , que é independente de uma configuração de escala SAP HANA ou scale-out.

Para mais informações, consulte "Verifique a conectividade com o SAP HANA - testHANAConnection" nas [ferramentas instantâneas da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-storage-connectivity"></a>Testar conectividade de armazenamento

O próximo passo de teste é verificar a conectividade com o armazenamento com base nos dados que colocou no ficheiro de configuração *HANABackupCustomerDetails.txt.* Em seguida, executar uma foto de teste. Antes de `azure_hana_backup` executar o comando, tem de fazer este teste. Para a sequência de comandos para este teste, consulte "Verifique a conectividade com o armazenamento - testStorageSnapshotConnection" nas [ferramentas instantâneas da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Após um sessão bem-sucedido nas interfaces de máquina virtual de armazenamento, o script continua com a fase 2 e cria um instantâneo de teste. A saída é mostrada aqui para uma configuração de escala de três nós de SAP HANA.

Se o instantâneo do teste for executado com sucesso com o script, pode agendar as imagens de armazenamento reais. Se não for bem sucedido, investigue os problemas antes de seguir em frente. O instantâneo do teste deve ficar por aqui até que as primeiras fotos verdadeiras estejam prontas.


### <a name="step-7-perform-snapshots"></a>Passo 7: Realizar instantâneos

Quando os passos de preparação estiverem terminados, pode começar a configurar e agendar os instantâneos de armazenamento reais. O guião a ser programado funciona com configurações de escala e escala SAP HANA. Para a execução periódica e regular do script de backup, agende o script utilizando o utilitário cron. 

Para obter a sintaxe e funcionalidade exatas de comando, consulte "Execute a cópia de segurança instantânea - azure_hana_backup" nas [ferramentas instantâneas da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

Quando o `azure_hana_backup` script corre, cria o instantâneo de armazenamento nas três fases seguintes:

1. Tem uma foto da SAP HANA.
1. Tem um instantâneo de armazenamento.
1. Remove o instantâneo SAP HANA que foi criado antes do instantâneo de armazenamento correr.

Para executar o script, chame-o da pasta executável do HDB para a qual foi copiado. 

O período de retenção é administrado com o número de instantâneos que são submetidos como parâmetro quando executa o script. A quantidade de tempo que é coberta pelos instantâneos de armazenamento é uma função do período de execução, e do número de instantâneos submetidos como parâmetro quando o script corre. 

Se o número de instantâneos que são mantidos exceder o número que são nomeados como parâmetro na chamada do script, o instantâneo de armazenamento mais antigo da mesma etiqueta é eliminado antes de um novo instantâneo ser executado. O número que dá como último parâmetro da chamada é o número que pode usar para controlar o número de instantâneos que são mantidos. Com este número, também pode controlar, indiretamente, o espaço do disco que é usado para instantâneos. 


## <a name="snapshot-strategies"></a>Estratégias instantâneas
A frequência de instantâneos para os diferentes tipos depende se utiliza a funcionalidade de recuperação de desastres hana large instance. Esta funcionalidade baseia-se em instantâneos de armazenamento, o que pode exigir recomendações especiais para os períodos de frequência e execução dos instantâneos de armazenamento. 

Nas considerações e recomendações que se seguem, o pressuposto é que *não* utiliza a funcionalidade de recuperação de desastres que a HANA Large Instances oferece. Em vez disso, utiliza as imagens de armazenamento para ter cópias de segurança e poderá fornecer uma recuperação pontual nos últimos 30 dias. Dadas as limitações do número de instantâneos e espaço, considere os seguintes requisitos:

- O tempo de recuperação para a recuperação do ponto no tempo.
- O espaço usado.
- O ponto de recuperação e os objetivos de tempo de recuperação para uma potencial recuperação de uma catástrofe.
- A eventual execução de backups de base de dados completas da HANA contra discos. Sempre que for efetuada uma cópia de segurança de base de dados completa contra discos ou a interface **backint,** a execução dos instantâneos de armazenamento falha. Se planeia executar cópias de segurança em cima de imagens de armazenamento, certifique-se de que a execução das imagens de armazenamento é desativada durante este período.
- O número de instantâneos por volume, que é limitado a 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Se não utilizar a funcionalidade de recuperação de desastres de GRANDES Instâncias HANA, o período de instantâneo é menos frequente. Nesses casos, efetue os instantâneos combinados em /hana/data e /hana/shared, que inclui /usr/seiva, em períodos de 12 horas ou 24 horas. Guarde as fotos por um mês. O mesmo se aplica às imagens do volume de cópiade cópia de bordo. A execução de cópias de segurança de registo de transações SAP HANA contra o volume de cópia de segurança de registo ocorre em períodos de 5 minutos a 15 minutos.

Os instantâneos de armazenamento programados são melhor realizados usando cron. Use o mesmo guião para todas as necessidades de recuperação de cópias de segurança e desastres. Modifique as inputs do script para corresponder aos vários tempos de backup solicitados. Estas fotos são todas programadas de forma diferente em cron, dependendo do seu tempo de execução. Pode ser de hora em hora, a cada 12 horas, diariamente ou semanalmente. 

O exemplo seguinte mostra uma programação cronememem em /etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
No exemplo anterior, um instantâneo combinado de hora a hora cobre os volumes que contêm o /hana/data e /hana/shared/SID, que inclui /usr/seiva, locais. Utilize este tipo de instantâneo para uma recuperação pontual mais rápida nos últimos dois dias. Há também uma foto diária nesses volumes. Então, você tem dois dias de cobertura por fotos de hora a hora mais quatro semanas de cobertura por fotos diárias. O volume de cópia de segurança do registo de transações também é apoiado diariamente. Estes reforços são mantidos por quatro semanas. 

Como pode ver na terceira linha de crontab, a cópia de segurança do registo de transações da HANA está programada para ser executada a cada 5 minutos. Os tempos de início dos diferentes trabalhos de compadrio que executam instantâneos de armazenamento são escalonados. Desta forma, as fotos não funcionam de uma só vez num determinado momento. 

No exemplo seguinte, executa um instantâneo combinado que cobre os volumes que contêm os /hana/data e /hana/shared/SID, que inclui /usr/seiva, localizações numa base horária. Guarde estas fotos por dois dias. As imagens dos volumes de cópia de segurança do registo de transações são executadas numa base de 5 minutos e são mantidas durante quatro horas. Como antes, a cópia de segurança do ficheiro de registo de transações HANA está programada para ser executada a cada 5 minutos. 

O instantâneo do volume de cópia de segurança do registo de transações é realizado com um atraso de 2 minutos após o início da cópia de segurança do registo de transações. Em circunstâncias normais, o registo de cópias de segurança da sap HANA termina nesses 2 minutos. Como antes, o volume que contém a bota LUN é apoiado uma vez por dia por um instantâneo de armazenamento e é mantido durante quatro semanas.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

O gráfico seguinte ilustra as sequências do exemplo anterior. A bota LUN está excluída.

![Relação entre backups e instantâneos](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

O SAP HANA realiza escritas regulares contra o volume /hana/log para documentar as alterações comprometidas na base de dados. Regularmente, o SAP HANA escreve um ponto de poupança para o volume /hana/data. Conforme especificado no crontab, uma cópia de segurança de registo de transações SAP HANA funciona a cada 5 minutos. 

Também vê que um instantâneo SAP HANA funciona de hora a hora como resultado de desencadear um instantâneo de armazenamento combinado sobre os volumes /hana/data e /hana/shared/SID. Após o instantâneo hana ter sucesso, o instantâneo combinado de armazenamento corre. Conforme instruído no crontab, o instantâneo de armazenamento no volume /hana/logbackup funciona a cada 5 minutos, cerca de 2 minutos após a cópia de segurança do registo de transações HANA.

> 

>[!IMPORTANT]
> A utilização de instantâneos de armazenamento para cópias de segurança SAP HANA só é valiosa quando as imagens são realizadas em conjunto com cópias de segurança de registo de transações SAP HANA. Estas cópias de segurança do registo de transações têm de cobrir os períodos de tempo entre os instantâneos de armazenamento. 

Se definiu um compromisso com os utilizadores de uma recuperação pontual de 30 dias, precisa de:

- Aceda a um instantâneo de armazenamento combinado sobre /hana/data e /hana/shared/SID que tenha 30 dias, em casos extremos. 
- Distenha cópias de segurança contíguas de registo de transações que cubram o tempo entre qualquer um dos instantâneos combinados de armazenamento. Assim, o instantâneo mais antigo do volume de cópia de segurança do registo de transações tem de ter 30 dias. Este não é o caso se copiar as cópias de registo de transações para outra parte da NFS que está localizada no Armazenamento Azure. Nesse caso, pode retirar cópias de segurança antigas do registo de transações daquela parte da NFS.

Para beneficiar de instantâneos de armazenamento e da eventual replicação de armazenamento de cópias de segurança de registo de transações, altere a localização para a qual o SAP HANA escreve as cópias de segurança do registo de transações. Podes fazer esta mudança no Hana Studio. 

Embora o SAP HANA recue automaticamente nos segmentos de registo completos, especifique um intervalo de cópia de segurança para ser determinista. Isto é especialmente verdade quando se utiliza a opção de recuperação de desastres, porque normalmente quer executar cópias de segurança de registo com um período determinístico. No caso seguinte, 15 minutos é definido como intervalo de backup de registo.

![Agendar registos de backup SAP HANA no Estúdio SAP HANA](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Também pode escolher backups mais frequentes do que a cada 15 minutos. Uma configuração mais frequente é frequentemente usada em conjunto com a funcionalidade de recuperação de desastres de GRANDES Instâncias HANA. Alguns clientes realizam cópias de segurança de registo de transações a cada 5 minutos.

Se a base de dados nunca tiver sido apoiada, o passo final é executar uma cópia de segurança baseada em ficheiros para criar uma única entrada de cópia de segurança que deve existir dentro do catálogo de cópias de segurança. Caso contrário, o SAP HANA não pode iniciar as suas cópias de segurança especificadas.

![Faça uma cópia de segurança baseada em ficheiros para criar uma única entrada de backup](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Após a execução dos seus primeiros instantâneos de armazenamento bem sucedidos, elimine o instantâneo de teste que correu no passo 6. Para obter mais informações, consulte "Remover instantâneos de teste - removaTestStorageSnapshot" nas [ferramentas instantâneas da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitorize o número e o tamanho das imagens no volume do disco

Num volume de armazenamento específico, pode monitorizar o número de instantâneos e o consumo de armazenamento desses instantâneos. O `ls` comando não mostra o diretório ou ficheiros instantâneos. O comando `du` Linux OS mostra detalhes sobre as fotos de armazenamento porque são armazenados nos mesmos volumes. Utilize o comando com as seguintes opções:

- `du –sh .snapshot`: Esta opção fornece um total de todas as imagens dentro do diretório instantâneo.
- `du –sh --max-depth=1`: Esta opção lista todos os instantâneos guardados na pasta **.snapshot** e o tamanho de cada instantâneo.
- `du –hc`: Esta opção fornece o tamanho total utilizado por todos os instantâneos.

Utilize estes comandos para se certificar de que as imagens que são tiradas e armazenadas não consomem todo o armazenamento nos volumes.

>[!NOTE]
>As fotos da bota LUN não são visíveis com os comandos anteriores.

### <a name="get-details-of-snapshots"></a>Obter detalhes de fotos
Para obter mais detalhes sobre as `azure_hana_snapshot_details`fotos, use o script . Pode executar este script em qualquer local se houver um servidor ativo no local de recuperação de desastres. O script fornece a seguinte saída, discriminada por cada volume que contém instantâneos: 
   * O tamanho dos instantâneos totais num volume
   * Os seguintes detalhes em cada instantâneo desse volume: 
      - Nome instantâneo 
      - Criar tempo 
      - Tamanho do instantâneo
      - Frequência do instantâneo
      - HANA Backup ID associado a esse instantâneo, se relevante

Para sintaxe do comando e saídas, consulte "List snapshots - azure_hana_snapshot_details" nas [ferramentas instantâneas da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Reduzir o número de instantâneos num servidor

Como anteriormente explicado, pode reduzir o número de certos rótulos de instantâneos que armazena. Os dois últimos parâmetros do comando para iniciar um instantâneo são a etiqueta e o número de instantâneos que pretende reter.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

No exemplo anterior, o rótulo instantâneo é **diáriohana**. O número de instantâneos com esta etiqueta a manter é **de 28**. Ao responder ao consumo de espaço em disco, é possível reduzir o número de instantâneos armazenados. Uma maneira fácil de reduzir o número de instantâneos para 15, por exemplo, é executar o script com o último parâmetro definido para **15:**

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Se executar o script com esta definição, o número de instantâneos, que inclui o novo instantâneo de armazenamento, é de 15. As 15 fotografias mais recentes são mantidas e as 15 fotografias mais antigas são apagadas.

 >[!NOTE]
 > Este script reduz o número de instantâneos apenas se houver instantâneos com mais de uma hora de idade. O guião não apaga fotografias com menos de uma hora de idade. Estas restrições estão relacionadas com a funcionalidade opcional de recuperação de desastres oferecida.

Se já não quiser manter um conjunto de instantâneos com o prefixo de reserva **diariamentehana** nos exemplos de sintaxe, execute o script com **0** como número de retenção. Todas as imagens que correspondem à etiqueta são removidas. Remover todos os instantâneos pode afetar as capacidades da funcionalidade de recuperação de desastres de grandes instâncias HANA.

Uma segunda opção para eliminar instantâneos `azure_hana_snapshot_delete`específicos é utilizar o script . Este script foi concebido para eliminar um instantâneo ou um conjunto de instantâneos, utilizando o ID de backup HANA, tal como se encontra no HANA Studio, ou através do próprio nome instantâneo. Atualmente, o ID de reserva está apenas ligado às imagens criadas para o tipo de instantâneo **hana.** As cópias de segurança instantâneas dos **registos** do tipo e da **bota** não executam uma foto do SAP HANA, por isso não há identificação de reserva para essas fotos. Se o nome instantâneo for introduzido, procura todos os instantâneos nos diferentes volumes que correspondam ao nome instantâneo introduzido. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Para obter mais informações sobre o script, consulte "Delete a snapshot - azure_hana_snapshot_delete" nas [ferramentas instantâneas da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Executar o script como **raiz**do utilizador .

>[!IMPORTANT]
>Se houver dados que existam apenas no instantâneo que planeia eliminar, depois de o instantâneo ser eliminado, esses dados perdem-se para sempre.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Restauro ao nível de ficheiro a partir de um instantâneo de armazenamento

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Para os tipos de instantâneos **hana** e **registos,** pode aceder diretamente às imagens nos volumes do diretório **.snapshot.** Há um subdiretório para cada uma das fotos. Copiar cada ficheiro no estado em que estava no ponto do instantâneo desse subdiretório para a estrutura real do diretório. 

Na versão atual do script, *não* há nenhum script de restauro fornecido para o restauro instantâneo como self-service. A restauração instantânea pode ser realizada como parte dos scripts de recuperação de desastres de autosserviço no local de recuperação de desastres durante a failover. Para restaurar um instantâneo desejado a partir dos instantâneos disponíveis, deve contactar a equipa de operações da Microsoft abrindo um pedido de serviço.

>[!NOTE]
>A restauração de ficheiros simples não funciona para fotos da bota LUN independentes do tipo de unidades HANA Large Instance. O diretório **.snapshot** não está exposto na bota LUN. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Recupere para o mais recente instantâneo da HANA

Num cenário de produção para baixo, o processo de recuperação de um instantâneo de armazenamento pode ser iniciado como um incidente com o Microsoft Azure Support. É uma questão de alta urgência se os dados foram apagados num sistema de produção e a única maneira de recuperá-lo é restaurar a base de dados de produção.

Numa situação diferente, uma recuperação pontual pode ser de baixa urgência e dias previstos com antecedência. Você pode planear esta recuperação com SAP HANA em Azure em vez de levantar uma bandeira de alta prioridade. Por exemplo, pode planear atualizar o software SAP aplicando um novo pacote de melhoramento. Em seguida, você precisa voltar a uma foto que representa o estado antes da atualização do pacote de melhoramento.

Antes de enviar o pedido, precisa se preparar. A equipa SAP HANA da Azure pode então lidar com o pedido e fornecer os volumes restaurados. Depois, restaure a base de dados HANA com base nas imagens.

Para obter um instantâneo restaurado com o novo conjunto de ferramentas, consulte "Como restaurar um instantâneo" no guia de [recuperação manual para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)a partir de um instantâneo de armazenamento .

Para se preparar para o pedido, siga estes passos.

1. Decida qual instantâneo restaurar. Apenas o volume hana/dados é restaurado a menos que instrua o contrário. 

1. Desligue a instância HANA.

   ![Desligue a instância HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Desmonte os volumes de dados em cada nó de base de dados HANA. Se os volumes de dados ainda estiverem montados no sistema operativo, a restauração do instantâneo falha.

   ![Desmontar os volumes de dados em cada nó de base de dados HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Abra um pedido de apoio azure e inclua instruções sobre a restauração de um instantâneo específico:

   - Durante a restauração: O SAP HANA no Serviço Azure pode pedir-lhe para assistir a uma chamada de conferência para coordenar, verificar e confirmar que o instantâneo de armazenamento correto é restaurado. 

   - Após a restauração: O SAP HANA no Serviço Azure notifica-o quando o instantâneo de armazenamento é restaurado.

1. Depois de concluído o processo de restauração, remonte todos os volumes de dados.

   ![Remontar todos os volumes de dados](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Outra possibilidade para obter, por exemplo, ficheiros de dados SAP HANA recuperados de um instantâneo de armazenamento, está documentado no passo 7 no guia de [recuperação manual para SAP HANA em Azure a partir de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Para restaurar a partir de uma cópia de segurança instantânea, consulte o guia de [recuperação manual para O SAP HANA em Azure a partir de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

>[!Note]
>Se o seu instantâneo foi restaurado pelas operações da Microsoft, não precisa de fazer o passo 7.


### <a name="recover-to-another-point-in-time"></a>Recuperar para outro ponto no tempo
Para restaurar um determinado ponto no tempo, consulte "Recuperar a base de dados para o seguinte ponto de tempo" no guia de [recuperação manual para SAP HANA em Azure a partir de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


## <a name="next-steps"></a>Passos seguintes
- Consulte [os princípios e a preparação](hana-concept-preparation.md)da recuperação de desastres.
