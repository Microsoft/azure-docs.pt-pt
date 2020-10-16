---
title: HANA backup e restauro em SAP HANA em Azure (Grandes Instâncias) / Microsoft Docs
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
ms.openlocfilehash: 79ef279423c524f0d409815e7ae163aa699f5428
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87082210"
---
# <a name="backup-and-restore-on-sap-hana-on-azure"></a>Backup e restauro em SAP HANA em Azure

>[!IMPORTANT]
>Este artigo não substitui a documentação da administração SAP HANA ou as Notas SAP. Esperamos que tenha uma compreensão sólida e experiência na administração e operações da SAP HANA, especialmente para apoio, restauro, elevada disponibilidade e recuperação de desastres. Neste artigo, são mostradas imagens do ESTÚDIO SAP HANA. O conteúdo, a estrutura e a natureza dos ecrãs das ferramentas de administração SAP e das próprias ferramentas podem mudar de lançamento SAP HANA para lançamento.

É importante que exerça passos e processos tomados no seu ambiente e com as suas versões e lançamentos HANA. Alguns processos descritos neste artigo são simplificados para uma melhor compreensão geral. Não devem ser usados como passos detalhados para eventuais manuais de operação. Se pretender criar manuais de operação para as suas configurações, teste e exerça os seus processos e documente os processos relacionados com as suas configurações específicas. 

Um dos aspetos mais importantes das bases de dados operacionais é protegê-las de acontecimentos catastróficos. A causa destes eventos pode ser qualquer coisa, desde desastres naturais a simples erros do utilizador.

O backup de uma base de dados, com a capacidade de a restaurar a qualquer ponto do tempo, como antes de alguém apagar dados críticos, permite a restauração de um estado que está o mais próximo possível da forma como estava antes da interrupção.

Devem ser realizados dois tipos de backups para conseguir a capacidade de restauro:

- Backups de bases de dados: Cópias de segurança completas, incrementais ou diferenciais
- Backups de registo de transações

Além das cópias de segurança completas realizadas a nível de aplicação, pode efetuar cópias de segurança com imagens de armazenamento. As imagens de armazenamento não substituem as cópias de segurança do registo de transações. As cópias de segurança dos registos de transações continuam a ser importantes para restaurar a base de dados a um determinado ponto no tempo ou para esvaziar os registos de transações já comprometidas. Os instantâneos de armazenamento podem acelerar a recuperação fornecendo rapidamente uma imagem roll-forward da base de dados. 

SAP HANA on Azure (Grandes Instâncias) oferece duas opções de backup e restauro:

- **Faça-o por si mesmo (DIY).** Depois de se certificar de que existe espaço suficiente em disco, execute a base de dados completa e faça cópias de segurança utilizando um dos seguintes métodos de backup do disco. Pode fazer uma culação direta aos volumes ligados às unidades HANA Large Instance ou às ações NFS que são criadas numa máquina virtual Azure (VM). Neste último caso, os clientes configuram um Linux VM em Azure, anexam o Azure Storage ao VM e partilham o armazenamento através de um servidor NFS configurado nesse VM. Se efetuar a cópia de segurança contra volumes que se ligam diretamente às unidades HANA Large Instance, copie as cópias de segurança para uma conta de armazenamento Azure. Faça isto depois de criar um Azure VM que exporta ações NFS baseadas no Azure Storage. Também pode utilizar um cofre Azure Backup ou um armazenamento frio Azure. 

   Outra opção é utilizar uma ferramenta de proteção de dados de terceiros para armazenar as cópias de segurança depois de copiadas para uma conta de armazenamento Azure. A opção de backup DIY também pode ser necessária para os dados que você precisa armazenar por longos períodos de tempo para fins de conformidade e auditoria. Em todos os casos, as cópias de segurança são copiadas em ações NFS representadas através de um VM e Azure Storage.

- **Backup de infraestrutura e restaurar a funcionalidade.** Também pode utilizar a funcionalidade de backup e restauro que a infraestrutura subjacente da SAP HANA em Azure (Grandes Instâncias) fornece. Esta opção satisfaz a necessidade de backups e restauros rápidos. O resto desta secção aborda a funcionalidade de backup e restauro que é oferecida com HANA Large Instances. Esta secção também cobre a relação que o backup e o restauro têm para a funcionalidade de recuperação de desastres oferecida pela HANA Large Instances.

> [!NOTE]
>   A tecnologia instantânea que é usada pela infraestrutura subjacente da HANA Large Instances tem uma dependência de instantâneos SAP HANA. Neste ponto, os instantâneos SAP HANA não funcionam em conjunto com vários inquilinos de recipientes de base de dados multitenntes SAP HANA. Se apenas um inquilino for implantado, os instantâneos SAP HANA funcionam e você pode usar este método.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Utilize instantâneos de armazenamento de SAP HANA em Azure (Grandes Instâncias)

A infraestrutura de armazenamento subjacente ao SAP HANA em Azure (Grandes Instâncias) suporta instantâneos de armazenamento de volumes. É suportada a cópia de segurança e a restauração de volumes, com as seguintes considerações:

- Em vez de cópias de segurança completas da base de dados, as imagens de volume de armazenamento são tiradas frequentemente.
- Quando um instantâneo é desencadeado sobre /hana/dados e /hana/shared, que inclui /usr/seiva, volumes, a tecnologia snapshot inicia um instantâneo SAP HANA antes de passar o instantâneo de armazenamento. Este snapshot SAP HANA é o ponto de configuração para eventuais restaurações de log após a recuperação do instantâneo de armazenamento. Para que uma foto da HANA seja bem sucedida, precisa de uma instância HANA ativa. Num cenário de HSR, um instantâneo de armazenamento não é suportado num nó secundário atual onde não pode ser realizado um instantâneo HANA.
- Após o instantâneo de armazenamento ser executado com sucesso, o instantâneo SAP HANA é eliminado.
- As cópias de segurança de registo de transações são tomadas frequentemente e armazenadas no volume /hana/logbackups ou no Azure. Pode ativar o volume de /hana/logbackups que contém as cópias de segurança do registo de transações para tirar uma fotografia separadamente. Nesse caso, não precisas de fazer uma foto da HANA.
- Se tiver de restaurar uma base de dados a um determinado ponto do tempo, para uma paragem de produção, solicite que o Microsoft Azure Support ou o SAP HANA em Azure restauem a uma determinada imagem de armazenamento. Um exemplo é a restauração planeada de um sistema de caixas de areia para o seu estado original.
- O snapshot SAP HANA que está incluído no instantâneo de armazenamento é um ponto de compensação para a aplicação de cópias de segurança de registo de transações que correram e foram armazenadas após a tomada do instantâneo de armazenamento.
- Estas cópias de segurança de registo de transações são tomadas para restaurar a base de dados de volta a um determinado ponto no tempo.

Pode executar instantâneos de armazenamento que visam três classes de volumes:

- Um instantâneo combinado sobre /hana/dados e /hana/shared, que inclui /usr/seiva. Este instantâneo requer a criação de um instantâneo SAP HANA como preparação para o instantâneo de armazenamento. O snapshot SAP HANA garante que a base de dados está num estado consistente do ponto de vista de armazenamento. Para o processo de restauro, é um ponto a definir.
- Uma imagem separada sobre /hana/logbackups.
- Uma divisória do sistema operativo.

Para obter os mais recentes scripts e documentação instantânea, consulte [o GitHub.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md) Quando descarrega o pacote de script snapshot do [GitHub,](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)obtém três ficheiros. Um dos ficheiros está documentado num PDF para a funcionalidade fornecida. Depois de descarregar o conjunto de ferramentas, siga as instruções em "Obter as ferramentas instantâneas".

## <a name="storage-snapshot-considerations"></a>Considerações de instantâneo de armazenamento

>[!NOTE]
>Os instantâneos de armazenamento consomem espaço de armazenamento que é atribuído às unidades de Grande Instância HANA. Considere os seguintes aspetos do agendamento de fotos de armazenamento e quantos instantâneos de armazenamento devem guardar. 

As mecânicas específicas dos instantâneos de armazenamento para SAP HANA em Azure (Grandes Instâncias) incluem:

- Uma foto de armazenamento específica no momento em que é tomada consome pouco armazenamento.
- À medida que o conteúdo dos dados muda e o conteúdo nos ficheiros de dados DO SAP HANA muda no volume de armazenamento, o instantâneo precisa de armazenar o conteúdo original do bloco e as alterações de dados.
- Como resultado, o instantâneo de armazenamento aumenta de tamanho. Quanto mais tempo o instantâneo existir, maior será o instantâneo de armazenamento.
- Quanto mais alterações forem feitas ao volume da base de dados SAP HANA ao longo da vida de um instantâneo de armazenamento, maior é o consumo de espaço do instantâneo de armazenamento.

O SAP HANA em Azure (Grandes Instâncias) vem com tamanhos de volume fixos para os dados SAP HANA e volumes de log. Fazer fotos desses volumes entra no seu espaço de volume. Tem de:

- Determinar quando agendar as fotos de armazenamento.
- Monitorize o consumo de espaço dos volumes de armazenamento. 
- Gerencie o número de fotos que armazena. 

Pode desativar as imagens de armazenamento quando importa massas de dados ou efetua outras alterações significativas na base de dados HANA. 


As seguintes secções fornecem informações para a realização destes instantâneos e incluem recomendações gerais:

- Embora o hardware possa sustentar 255 instantâneos por volume, você deseja ficar bem abaixo deste número. A recomendação é de 250 ou menos.
- Antes de efetuar as imagens de armazenamento, monitorize e mantenha o registo do espaço livre.
- Diminua o número de instantâneos de armazenamento com base no espaço livre. Pode baixar o número de instantâneos que guarda, ou pode estender os volumes. Pode encomendar armazenamento adicional em unidades de 1 terabyte.
- Durante atividades como a transferência de dados para o SAP HANA com ferramentas de migração da plataforma SAP (R3load) ou restaurar as bases de dados SAP HANA a partir de backups, desativar as imagens de armazenamento no volume /hana/dados. 
- Durante as reorganizações maiores das tabelas SAP HANA, evite instantâneos de armazenamento, se possível.
- Os instantâneos de armazenamento são um pré-requisito para tirar partido das capacidades de recuperação de desastres da SAP HANA em Azure (Grandes Instâncias).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Pré-requisitos para a utilização de instantâneos de armazenamento de autosserviço

Para se certificar de que o script snapshot funciona com sucesso, certifique-se de que o Perl está instalado no sistema operativo Linux no servidor HANA Large Instances. Perl vem pré-instalado na sua unidade HANA Large Instance. Para verificar a versão Perl, utilize o seguinte comando:

`perl -v`

![A chave pública é copiada executando este comando](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Configurar instantâneos de armazenamento

Para configurar instantâneos de armazenamento com HANA Large Instances, siga estes passos.
1. Certifique-se de que o Perl está instalado no sistema operativo Linux no servidor HANA Large Instances.
1. Modificar o /etc/ssh/ssh \_ config para adicionar a linha _MACs hmac-sha1_.
1. Crie uma conta de utilizador de backup SAP HANA no nó principal para cada instância SAP HANA que executar, se aplicável.
1. Instale o cliente SAP HANA HDB em todos os servidores SAP HANA Large Instances.
1. No primeiro servidor SAP HANA Large Instances de cada região, crie uma chave pública para aceder à infraestrutura de armazenamento subjacente que controla a criação de instantâneos.
1. Copie os scripts e o ficheiro de configuração do [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) para a localização do **hdbsql** na instalação SAP HANA.
1. Modifique o ficheiro *HANABackupDetails.txt* conforme necessário para as especificações adequadas do cliente.

Obtenha os mais recentes scripts instantâneos e documentação do [GitHub.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) Para os passos listados anteriormente, consulte [as ferramentas instantâneas da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

### <a name="consideration-for-mcod-scenarios"></a>Consideração para cenários MCOD
Se executar um [cenário MCOD](https://launchpad.support.sap.com/#/notes/1681092) com múltiplas instâncias SAP HANA numa unidade HANA Large Instance, tem volumes de armazenamento separados previstos para cada uma das instâncias SAP HANA. Para obter mais informações sobre o MDC e outras considerações, consulte "Coisas importantes a lembrar" nas [ferramentas instantâneas da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Passo 1: Instalar o cliente SAP HANA HDB

O sistema operativo Linux instalado no SAP HANA em Azure (Grandes Instâncias) inclui as pastas e scripts necessários para executar instantâneos de armazenamento SAP HANA para fins de backup e recuperação de desastres. Verifique se há lançamentos mais recentes no [GitHub.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 

É da sua responsabilidade instalar o cliente SAP HANA HDB nas unidades HANA Large Instance enquanto instala o SAP HANA.

### <a name="step-2-change-the-etcsshssh_config"></a>Passo 2: Alterar o /etc/ssh/ssh \_ config

Este passo é descrito em "Enable communication with storage" em [ferramentas instantâneas da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-3-create-a-public-key"></a>Passo 3: Criar uma chave pública

Para permitir o acesso às interfaces instantâneas de armazenamento do seu inquilino HANA Large Instance, estabeleça um procedimento de inscrição através de uma chave pública. 

No primeiro servidor SAP HANA on Azure (Grandes Instâncias) no seu inquilino, crie uma chave pública para aceder à infraestrutura de armazenamento. Com uma chave pública, uma palavra-passe não é necessária para iniciar sedudas nas interfaces de instantâneo de armazenamento. Também não precisa de manter credenciais de senha com uma chave pública. 

Para gerar uma chave pública, consulte "Ativar a comunicação com o armazenamento" em [ferramentas instantâneas da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-4-create-an-sap-hana-user-account"></a>Passo 4: Criar uma conta de utilizador SAP HANA

Para iniciar a criação de snapshots SAP HANA, crie uma conta de utilizador em SAP HANA que os scripts de instantâneo de armazenamento possam usar. Crie uma conta de utilizador SAP HANA no Estúdio SAP HANA para este fim. O utilizador deve ser criado sob o SYSTEMDB e *não* na base de dados SID para MDC. No ambiente de contentores únicos, o utilizador é criado na base de dados do arrendatário. Esta conta deve ter **privilégios de Backup Admin** e **Catalog Read.** 

Para configurar e utilizar uma conta de utilizador, consulte "Ative a comunicação com o SAP HANA" no [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Passo 5: Autorizar a conta de utilizador SAP HANA

Neste passo, autoriza a conta de utilizador SAP HANA que criou para que os scripts não necessitem de enviar senhas no tempo de execução. O comando SAP HANA `hdbuserstore` permite a criação de uma chave de utilizador SAP HANA. A chave é armazenada em um ou mais nós SAP HANA. A chave do utilizador permite ao utilizador aceder ao SAP HANA sem ter de gerir palavras-passe dentro do processo de script. O processo de scripting é discutido mais tarde neste artigo.

>[!IMPORTANT]
>Executar estes comandos de configuração com o mesmo contexto de utilizador em que os comandos instantâneos são executados. Caso contrário, os comandos instantâneos não funcionarão corretamente.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Passo 6: Obtenha os scripts instantâneos, configuure os instantâneos e teste a configuração e conectividade

Descarregue a versão mais recente dos scripts do [GitHub.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) A forma como os scripts são instalados mudou com o lançamento 4.1 dos scripts. Para obter mais informações, consulte "Enable communication with SAP HANA" em [ferramentas instantâneas da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Para obter a sequência exata de comandos, consulte "Fácil instalação de ferramentas instantâneas (predefinida)" nas [ferramentas de instantâneo da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Recomendamos a utilização da instalação predefinida. 

Para atualizar da versão 3.x para 4.1, consulte "Atualize uma instalação existente" nas [ferramentas instantâneas da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Para desinstalar o conjunto de ferramentas 4.1, consulte "Desinstalação das ferramentas instantâneas" nas [ferramentas instantâneas da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Não se esqueça de executar os passos descritos em "Configuração completa de ferramentas instantâneas" em [ferramentas de instantâneo da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

O propósito dos diferentes scripts e ficheiros à medida que foram instalados é descrito em "O que são estas ferramentas instantâneas?" em [ferramentas instantâneas da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Antes de configurar as ferramentas instantâneas, certifique-se de que também configura corretamente as localizações e configurações de backup HANA. Para obter mais informações, consulte "SAP HANA Configuration" em [ferramentas instantâneas da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

A configuração do conjunto de ferramentas snapshot é descrita em "Config file - HANABackupCustomerDetails.txt" em [ferramentas instantâneas da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-connectivity-with-sap-hana"></a>Testar conectividade com SAP HANA

Depois de colocar todos os dados de configuração no ficheiro *HANABackupCustomerDetails.txt, * verifique se as configurações estão corretas para os dados da instância HANA. Utilize o script `testHANAConnection` , que é independente de uma configuração de escala SAP HANA ou de escala.

Para obter mais informações, consulte "Verifique a conectividade com o SAP HANA - testHANAConnection" em [ferramentas instantâneas da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-storage-connectivity"></a>Conectividade de armazenamento de teste

O próximo passo do teste é verificar a conectividade com o armazenamento com base nos dados que colocou no ficheiro de configuração *HANABackupCustomerDetails.txt.* Em seguida, executar uma foto de teste. Antes de executar o `azure_hana_backup` comando, tem de fazer este teste. Para a sequência de comandos para este teste, consulte "Verifique a conectividade com o armazenamento - testStorageSnapshotConnection" nas [ferramentas instantâneas da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Após um sucesso de sindução nas interfaces da máquina virtual de armazenamento, o script continua com a fase 2 e cria um instantâneo de teste. A saída é mostrada aqui para uma configuração de escala de três nós de SAP HANA.

Se o instantâneo do teste for executado com sucesso com o script, pode agendar as imagens de armazenamento reais. Se não for bem sucedido, investigue os problemas antes de seguir em frente. O instantâneo do teste deve ficar por perto até que as primeiras fotos reais sejam feitas.


### <a name="step-7-perform-snapshots"></a>Passo 7: Realizar instantâneos

Quando os passos de preparação estiverem terminados, pode começar a configurar e agendar as fotos de armazenamento reais. O script a ser programado funciona com configurações de escala SAP HANA e escala-out. Para execução periódica e regular do script de backup, marque o script utilizando o utilitário cron. 

Para a sintaxe e funcionalidade exata do comando, consulte "Execute a cópia de segurança instantânea - azure_hana_backup" nas [ferramentas instantâneas da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

Quando o script `azure_hana_backup` é executado, cria o instantâneo de armazenamento nas três fases seguintes:

1. Tem uma foto da SAP HANA.
1. Tem uma foto de armazenamento.
1. Remove o instantâneo SAP HANA que foi criado antes do instantâneo de armazenamento ser publicado.

Para executar o script, chame-o da pasta executável HDB para a qual foi copiado. 

O período de retenção é administrado com o número de instantâneos que são submetidos como parâmetro quando executam o script. A quantidade de tempo que é coberta pelos instantâneos de armazenamento é uma função do período de execução, e do número de instantâneos submetidos como parâmetro quando o script é executado. 

Se o número de instantâneos que são mantidos exceder o número que são nomeados como parâmetro na chamada do script, a imagem de armazenamento mais antiga da mesma etiqueta é eliminada antes de um novo instantâneo ser executado. O número que dá como último parâmetro da chamada é o número que pode utilizar para controlar o número de instantâneos que são mantidos. Com este número, também pode controlar, indiretamente, o espaço em disco que é usado para instantâneos. 


## <a name="snapshot-strategies"></a>Estratégias instantâneas
A frequência de instantâneos para os diferentes tipos depende se utiliza a funcionalidade de recuperação de desastres HANA Large Instance. Esta funcionalidade baseia-se em instantâneos de armazenamento, que podem exigir recomendações especiais para os períodos de frequência e execução dos instantâneos de armazenamento. 

Nas considerações e recomendações que se seguem, o pressuposto é que *você não* usa a funcionalidade de recuperação de desastres que a HANA Large Instances oferece. Em vez disso, utiliza as imagens de armazenamento para ter cópias de segurança e poderá fornecer recuperação pontual nos últimos 30 dias. Dadas as limitações do número de instantâneos e espaço, considere os seguintes requisitos:

- O tempo de recuperação para recuperação pontual.
- O espaço usado.
- O ponto de recuperação e o tempo de recuperação para a recuperação potencial de uma catástrofe.
- A eventual execução de cópias de dados da HANA contra discos. Sempre que for realizada uma cópia de segurança completa da base de dados contra discos ou a interface **de backint,** a execução das imagens de armazenamento falha. Se planeia executar cópias de dados completas em cima de imagens de armazenamento, certifique-se de que a execução das imagens de armazenamento está desativada durante este período.
- O número de instantâneos por volume, que é limitado a 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Se não utilizar a funcionalidade de recuperação de desastres da HANA Large Instances, o período de instantâneo é menos frequente. Nesses casos, execute as imagens combinadas em /hana/data e /hana/shared, que inclui /usr/seiva, em períodos de 12 horas ou 24 horas. Guarde as fotos por um mês. O mesmo se aplica às imagens do volume de backup de registo. A execução de cópias de segurança de registo de transações SAP HANA contra o volume de backup de registo ocorre em períodos de 5 minutos a 15 minutos.

As imagens de armazenamento programadas são melhor executadas utilizando cron. Use o mesmo script para todas as cópias de segurança e necessidades de recuperação de desastres. Modifique as entradas do script para corresponder aos vários tempos de backup solicitados. Estes instantâneos são programados de forma diferente em cron, dependendo do tempo de execução. Pode ser de hora a hora, a cada 12 horas, diariamente ou semanalmente. 

O exemplo a seguir mostra um cron schedule em /etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
No exemplo anterior, um instantâneo combinado de hora em hora cobre os volumes que contêm o /hana/data e /hana/shared/SID, que inclui /usr/seiva, localizações. Utilize este tipo de instantâneo para uma recuperação pontual mais rápida nos últimos dois dias. Há também uma foto diária nesses volumes. Então, você tem dois dias de cobertura por instantâneos de hora a hora mais quatro semanas de cobertura por instantâneos diários. O volume de backup de registo de transações também é apoiado diariamente. Estes reforços são mantidos por quatro semanas. 

Como pode ver na terceira linha de crontab, a cópia de segurança do registo de transações HANA está programada para ser executada a cada 5 minutos. Os tempos iniciais dos diferentes cron jobs que executam os instantâneos de armazenamento são escalonados. Desta forma, as fotos não funcionam todas de uma vez a certa altura. 

No exemplo seguinte, executa-se uma imagem combinada que cobre os volumes que contêm o /hana/data e /hana/shared/SID, que inclui /usr/seiva, localizações de hora a hora. Guarde estas fotos por dois dias. As imagens dos volumes de backup do registo de transações são executadas numa base de 5 minutos e são mantidas durante quatro horas. Como antes, a cópia de segurança do ficheiro de registo de transações HANA está programada para ser executada a cada 5 minutos. 

A imagem instantânea do volume de backup do registo de transações é realizada com um atraso de 2 minutos após o início da cópia de segurança do registo de transações. Em circunstâncias normais, o registo de transações SAP HANA termina dentro desses 2 minutos. Como antes, o volume que contém a bota LUN é apoiado uma vez por dia por um instantâneo de armazenamento e é mantido por quatro semanas.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

O gráfico que se segue ilustra as sequências do exemplo anterior. A bota LUN está excluída.

![Relação entre backups e instantâneos](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

A SAP HANA executa gravações regulares contra o volume /hana/log para documentar as alterações comprometidas na base de dados. Regularmente, a SAP HANA escreve um ponto de poupança para o volume /hana/data. Conforme especificado no crontab, uma cópia de segurança do registo de transações SAP HANA é executado a cada 5 minutos. 

Você também vê que um snapshot SAP HANA é executado a cada hora como resultado de desencadear uma snapshot de armazenamento combinado sobre os volumes /hana/data e /hana/shared/SID volumes. Após o instantâneo HANA ter sucesso, o instantâneo combinado de armazenamento corre. Como indicado no crontab, o instantâneo de armazenamento no volume /hana/logbackup é executado a cada 5 minutos, cerca de 2 minutos após a cópia de segurança do registo de transações HANA.

> 

>[!IMPORTANT]
> A utilização de instantâneos de armazenamento para cópias de segurança SAP HANA só é valiosa quando as imagens são realizadas em conjunto com as cópias de segurança do registo de transações SAP HANA. Estas cópias de segurança de registo de transações precisam de cobrir os períodos de tempo entre as imagens de armazenamento. 

Se estabeleceu um compromisso com os utilizadores de uma recuperação pontual de 30 dias, precisa:

- Aceda a um instantâneo de armazenamento combinado sobre /hana/data e /hana/shared/SID que tem 30 dias, em casos extremos. 
- Tenha cópias de segurança contíguas de registo de transações que cubram o tempo entre qualquer uma das imagens combinadas de armazenamento. Assim, a foto mais antiga do volume de backup de registo de transações precisa de ter 30 dias de idade. Este não é o caso se copiar as cópias de segurança do registo de transações para outra ação da NFS localizada no Azure Storage. Nesse caso, podes fazer cópias de segurança antigas desse registo de transações dessa parte da NFS.

Para beneficiar de instantâneos de armazenamento e da eventual replicação de armazenamento de cópias de segurança de registo de transações, altere a localização para a qual a SAP HANA escreve as cópias de segurança do registo de transações. Podes fazer esta mudança no Estúdio HANA. 

Embora o SAP HANA faça o backup de segmentos completos de registos automaticamente, especifique um intervalo de backup de registo para ser determinista. Isto é especialmente verdade quando se utiliza a opção de recuperação de desastres porque normalmente pretende executar backups de registo com um período determinístico. No caso seguinte, 15 minutos são definidos como o intervalo de reserva de registo.

![Agendar registos de backup SAP HANA no ESTÚDIO SAP HANA](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Também pode escolher backups mais frequentes do que a cada 15 minutos. Um ajuste mais frequente é frequentemente usado em conjunto com a funcionalidade de recuperação de desastres de HANA Large Instances. Alguns clientes efetuam cópias de segurança de registo de transações a cada 5 minutos.

Se a base de dados nunca tiver sido apoiada, o passo final é realizar uma cópia de segurança da base de dados baseada em ficheiros para criar uma única entrada de backup que deve existir dentro do catálogo de cópias de segurança. Caso contrário, a SAP HANA não pode iniciar as suas cópias de segurança de registo especificadas.

![Faça uma cópia de segurança baseada em ficheiros para criar uma única entrada de backup](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Após a execução dos seus primeiros instantâneos de armazenamento bem sucedidos, elimine o instantâneo de teste que correu no passo 6. Para obter mais informações, consulte "Remover as fotos do teste - removaTestStorageSnapshot" em [ferramentas instantâneas da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitorize o número e o tamanho das imagens no volume do disco

Num volume de armazenamento específico, pode monitorizar o número de instantâneos e o consumo de armazenamento desses instantâneos. O `ls` comando não mostra o diretório ou ficheiros instantâneos. O comando Linux OS `du` mostra detalhes sobre as fotos de armazenamento porque estão armazenadas nos mesmos volumes. Utilize o comando com as seguintes opções:

- `du –sh .snapshot`: Esta opção fornece um total de todas as imagens instantâneas dentro do diretório de instantâneos.
- `du –sh --max-depth=1`: Esta opção lista todas as imagens que são guardadas na pasta **.snapshot** e o tamanho de cada instantâneo.
- `du –hc`: Esta opção fornece o tamanho total utilizado por todos os instantâneos.

Utilize estes comandos para se certificar de que as imagens que são tiradas e armazenadas não consomem todo o armazenamento nos volumes.

>[!NOTE]
>As fotos da bota LUN não são visíveis com os comandos anteriores.

### <a name="get-details-of-snapshots"></a>Obtenha detalhes de fotos
Para obter mais detalhes sobre as fotos, use o script `azure_hana_snapshot_details` . Pode executar este script em qualquer local se houver um servidor ativo no local de recuperação de desastres. O script fornece a seguinte saída, dividida por cada volume que contém instantâneos: 
   * O tamanho do total de instantâneos em um volume
   * Os seguintes detalhes em cada instantâneo desse volume: 
      - Nome instantâneo 
      - Criar tempo 
      - Tamanho do instantâneo
      - Frequência do instantâneo
      - ID de backup HANA associado a esse instantâneo, se relevante

Para sintaxe do comando e saídas, consulte "List snapshots - azure_hana_snapshot_details" em [ferramentas instantâneas da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Reduzir o número de instantâneos num servidor

Como explicado anteriormente, pode reduzir o número de certos rótulos de instantâneos que armazena. Os dois últimos parâmetros do comando para iniciar uma imagem são o rótulo e o número de instantâneos que pretende reter.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

No exemplo anterior, a etiqueta snapshot é **dailyhana**. O número de instantâneos com esta etiqueta a conservar é **de 28**. À medida que responde ao consumo de espaço em disco, é possível que queira reduzir o número de instantâneos armazenados. Uma maneira fácil de reduzir o número de instantâneos para 15, por exemplo, é executar o script com o último parâmetro definido para **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Se executar o script com esta definição, o número de instantâneos, que inclui o novo instantâneo de armazenamento, é de 15. Os 15 instantâneos mais recentes são mantidos, e os 15 instantâneos mais antigos são eliminados.

 >[!NOTE]
 > Este script reduz o número de instantâneos apenas se houver instantâneos com mais de uma hora de idade. O guião não apaga instantâneos com menos de uma hora de idade. Estas restrições estão relacionadas com a funcionalidade opcional de recuperação de desastres oferecida.

Se já não pretender manter um conjunto de instantâneos com o prefixo de backup **dailyhana** nos exemplos de sintaxe, execute o script com **0** como número de retenção. Todas as imagens que correspondem à etiqueta são então removidas. A remoção de todos os instantâneos pode afetar as capacidades da funcionalidade de recuperação de desastres HANA Large Instances.

Uma segunda opção para eliminar instantâneos específicos é utilizar o script `azure_hana_snapshot_delete` . Este script foi concebido para eliminar um instantâneo ou conjunto de instantâneos, utilizando o ID de backup HANA, tal como encontrado no Estúdio HANA ou através do próprio nome instantâneo. Atualmente, o ID de backup está apenas ligado aos instantâneos criados para o tipo de instantâneo **hana.** As cópias de segurança instantâneas dos **registos** e **botas** não realizam uma foto do SAP HANA, por isso não há identificação de reserva para essas fotos. Se o nome do instantâneo for introduzido, procura todas as imagens dos diferentes volumes que correspondam ao nome do instantâneo introduzido. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Para obter mais informações sobre o script, consulte "Delete a snapshot - azure_hana_snapshot_delete" em [ferramentas de instantâneo da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Executar o script como **raiz**do utilizador .

>[!IMPORTANT]
>Se houver dados que existam apenas no instantâneo que pretende eliminar, depois de o instantâneo ser eliminado, esses dados perdem-se para sempre.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Restauro do nível de ficheiro a partir de um instantâneo de armazenamento

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Para os tipos de **instantâneo hana** e **registos,** pode aceder diretamente aos instantâneos nos volumes do **diretório .snapshot.** Há uma subdireção para cada uma das fotos. Copie cada ficheiro no estado em que estava no ponto da foto desse subdiretório para a estrutura real do diretório. 

Na versão atual do script, não há *nenhum* script de restauro fornecido para a restauração do instantâneo como self-service. A restauração instantânea pode ser realizada como parte dos scripts de recuperação de desastres de autosserviço no local de recuperação de desastres durante o failover. Para restaurar uma imagem desejada a partir das imagens existentes, deve contactar a equipa de operações da Microsoft abrindo um pedido de serviço.

>[!NOTE]
>A restauração de ficheiros únicos não funciona para instantâneos da bota LUN independentemente do tipo de unidades HANA Large Instance. O **diretório .snapshot** não está exposto na bota LUN. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Recupere para o mais recente instantâneo HANA

Num cenário de produção para baixo, o processo de recuperação de um instantâneo de armazenamento pode ser iniciado como um incidente de cliente com o Microsoft Azure Support. É uma questão de alta urgência se os dados foram eliminados num sistema de produção e a única maneira de recuperá-lo é restaurar a base de dados de produção.

Numa situação diferente, uma recuperação pontual pode ser de baixa urgência e planeada com dias de antecedência. Você pode planear esta recuperação com SAP HANA em Azure em vez de levantar uma bandeira de alta prioridade. Por exemplo, pode planear atualizar o software SAP aplicando um novo pacote de melhoria. Em seguida, você precisa reverter para um instantâneo que representa o estado antes da atualização do pacote de melhoramento.

Antes de enviar o pedido, tem de se preparar. A equipa SAP HANA on Azure pode então lidar com o pedido e fornecer os volumes restaurados. Depois, restaura a base de dados HANA com base nas fotos.

Para obter um instantâneo restaurado com o novo conjunto de ferramentas, consulte "Como restaurar uma imagem" no [guia de recuperação manual para SAP HANA on Azure a partir de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Para preparar o pedido, siga estes passos.

1. Decida qual instantâneo restaurar. Apenas o volume de hana/dados é restaurado a menos que instrua o contrário. 

1. Desligue o caso HANA.

   ![Desligue a instância HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Desmonte os volumes de dados em cada nó de base de dados HANA. Se os volumes de dados ainda estiverem montados no sistema operativo, a restauração do instantâneo falha.

   ![Desmonte os volumes de dados em cada nó de base de dados HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Abra um pedido de suporte Azure e inclua instruções sobre a restauração de um instantâneo específico:

   - Durante a restauração: SAP HANA on Azure Service pode pedir-lhe para assistir a uma chamada de conferência para coordenar, verificar e confirmar que o instantâneo de armazenamento correto está restaurado. 

   - Após a restauração: O SAP HANA no Serviço Azure notifica-o quando a fotografia de armazenamento é restaurada.

1. Após o processo de restauração estar concluído, remonte todos os volumes de dados.

   ![Remonte todos os volumes de dados](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Outra possibilidade de obter, por exemplo, ficheiros de dados SAP HANA recuperados de um instantâneo de armazenamento, é documentada no passo 7 no [guia de recuperação manual para SAP HANA em Azure a partir de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Para restaurar a partir de uma cópia de segurança instantânea, consulte [o guia de recuperação manual para SAP HANA em Azure a partir de uma foto de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

>[!Note]
>Se a sua fotografia foi restaurada pelas operações da Microsoft, não precisa de fazer o passo 7.


### <a name="recover-to-another-point-in-time"></a>Recuperar para outro ponto no tempo
Para restaurar a um determinado ponto do tempo, consulte "Recuperar a base de dados até ao ponto seguinte no tempo" no [guia de recuperação manual para SAP HANA em Azure a partir de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


## <a name="next-steps"></a>Passos seguintes
- Consulte [os princípios de recuperação de desastres e a preparação.](hana-concept-preparation.md)
