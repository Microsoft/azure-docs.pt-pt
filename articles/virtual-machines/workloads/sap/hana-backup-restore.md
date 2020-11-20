---
title: HANA backup e restauro em SAP HANA em Azure (Grandes Instâncias) / Microsoft Docs
description: Como executar backup HANA e restaurar em SAP HANA em Azure (Grandes Instâncias)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42333a3feae19b6c3c77494f7e843cac1d9bc078
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968133"
---
# <a name="backup-and-restore-of-sap-hana-on-hana-large-instances"></a>Backup e restauro de SAP HANA em Grandes Instâncias HANA

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

Depois de colocar todos os dados de configuração no ficheiro *HANABackupCustomerDetails.txt,* verifique se as configurações estão corretas para os dados da instância HANA. Utilize o script `testHANAConnection` , que é independente de uma configuração de escala SAP HANA ou de escala.

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

Executar o script como **raiz** do utilizador .

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





## <a name="snapcenter-integration-in-sap-hana-large-instances"></a>Integração do SnapCenter em GRANDES instâncias SAP HANA

Esta secção descreve como os clientes podem usar o software NetApp SnapCenter para tirar uma foto, cópia de segurança e restaurar as bases de dados SAP HANA hospedadas no Microsoft Azure HANA Large Instances (HLI). 

O SnapCenter oferece soluções para cenários como backup/recuperação, recuperação de desastres (DR) com replicação de armazenamento assíncronos, replicação do sistema e clonagem de sistemas. Integrados com a SAP HANA Large Instances on Azure, os clientes podem agora utilizar o SnapCenter para operações de backup e recuperação.

Para obter referências adicionais, consulte NetApp TR-4614 e TR-4646 no SnapCenter.

- [SAP HANA Backup/Recuperação com SnapCenter (TR-4614)](https://www.netapp.com/us/media/tr-4614.pdf)
- [Recuperação de desastres SAP HANA com replicação de armazenamento (TR-4646)](https://www.netapp.com/us/media/tr-4646.pdf)
- [SAP HANA HSR com SnapCenter (TR-4719)](https://www.netapp.com/us/media/tr-4719.pdf)
- [Clonagem SAP do SnapCenter (TR-4667)](https://www.netapp.com/us/media/tr-4667.pdf)

### <a name="system-requirements-and-prerequisites"></a>Requisitos do sistema e pré-requisitos

Para executar o SnapCenter no Azure HLI, os requisitos do sistema incluem:
* SnapCenter Server no Azure Windows 2016 ou mais recente com 4-vCPU, RAM de 16 GB e um mínimo de 650 GB gerido armazenamento SSD premium.
* Sistema SAP HANA Large Instances com 1.5 TB – 24-TB RAM. Recomenda-se a utilização de dois sistemas SAP HANA Large Instance para operações de clonagem e testes.

Os passos para integrar o SnapCenter no SAP HANA são: 

1. Levante um pedido de bilhete de apoio para comunicar a chave pública gerada pelo utilizador à equipa do Microsoft Ops. Isto é necessário para configurar o utilizador Do SnapCenter para aceder ao sistema de armazenamento.
1. Crie um VM no seu VNET que tenha acesso ao HLI; este VM é utilizado para o SnapCenter. 
1. Descarregue e instale o SnapCenter. 
1. Operações de apoio e recuperação. 

### <a name="create-a-support-ticket-for-user-role-storage-setup"></a>Crie um bilhete de apoio para a configuração de armazenamento de funções de utilizador

1. Abra o portal Azure e navegue para a página **subscrições.** Uma vez na página "Subscrições", selecione a sua assinatura SAP HANA, delineada a vermelho abaixo.

   :::image type="content" source="./media/snapcenter/create-support-case-for-user-role-storage-setup.png" alt-text="Criar caso de suporte para a configuração do armazenamento do utilizador":::

1. Na sua página de subscrição SAP HANA, selecione a sub página **grupos de recursos.**

   :::image type="content" source="./media/snapcenter/solution-lab-subscription-resource-groups.png" alt-text="Grupo de recursos de subscrição de laboratório de solução" lightbox="./media/snapcenter/solution-lab-subscription-resource-groups.png":::

1. Selecione um grupo de recursos apropriado numa região.

   :::image type="content" source="./media/snapcenter/select-appropriate-resource-group-in-region.png" alt-text="Selecione o grupo de recursos apropriado na região" lightbox="./media/snapcenter/select-appropriate-resource-group-in-region.png":::

1. Selecione uma entrada SKU correspondente ao SAP HANA no armazenamento Azure.

   :::image type="content" source="./media/snapcenter/select-sku-entry-corresponding-to-sap-hana.png" alt-text="Selecione a entrada SKU correspondente ao SAP HANA" lightbox="./media/snapcenter/select-sku-entry-corresponding-to-sap-hana.png":::

1. Abra um novo pedido **de bilhete de apoio,** delineado a vermelho.

   :::image type="content" source="./media/snapcenter/open-new-support-ticket-request.png" alt-text="Abrir novo pedido de bilhete de apoio":::

1. No **separador Básicos,** forneça as seguintes informações para o bilhete:

   * **Tipo de emissão:** Técnico
   * **Assinatura:** A sua subscrição
   * **Serviço:** SAP HANA Grande Instância
   * **Recurso:** O seu grupo de recursos
   * **Resumo:** Fornecer a chave pública gerada pelo utilizador
   * **Tipo de problema:** Configuração e Configuração
   * **Subtipo de problema:** Configurar o SnapCenter para HLI


1. Na **Descrição** do bilhete de apoio, no separador **Detalhes,** forneça: 
   
   * Configurar o SnapCenter para HLI
   * A sua chave pública para o utilizador do SnapCenter (snapcenter.pem) - veja a chave pública criar exemplo abaixo

     :::image type="content" source="./media/snapcenter/new-support-request-details.png" alt-text="Novo pedido de suporte - Separador de detalhes" lightbox="./media/snapcenter/new-support-request-details.png":::

1. Selecione **Review + crie** para rever o seu bilhete de suporte. 

1. Gere um certificado para o nome de utilizador do SnapCenter no HANA Large Instance ou em qualquer servidor Linux.

   O SnapCenter requer um nome de utilizador e uma palavra-passe para aceder à máquina virtual de armazenamento (SVM) e para criar instantâneos da base de dados HANA. A Microsoft utiliza a chave pública para permitir que você (o cliente) desem te desafine a palavra-passe para aceder ao sistema de armazenamento.

   ```bash
   openssl req -x509 -nodes -days 1095 -newkey rsa:2048 -keyout snapcenter.key -out snapcenter.pem -subj "/C=US/ST=WA/L=BEL/O=NetApp/CN=snapcenter"
   Generating a 2048 bit RSA private key
   ................................................................................................................................................+++++
   ...............................+++++
   writing new private key to 'snapcenter.key'
   -----

   sollabsjct31:~ # ls -l cl25*
   -rw-r--r-- 1 root root 1704 Jul 22 09:59 snapcenter.key
   -rw-r--r-- 1 root root 1253 Jul 22 09:59 snapcenter.pem

   ```

1. Prenda o ficheiro snapcenter.pem ao bilhete de suporte e, em seguida, selecione **Criar**

   Assim que o certificado de chave pública for submetido, a Microsoft configura o nome de utilizador do SnapCenter para o seu inquilino juntamente com o endereço IP SVM.   

1. Depois de receber o IP SVM, desajuste uma palavra-passe para aceder ao SVM, que controla.

   Segue-se um exemplo da CHAMADA DE REPOUSO (documentação) da HANA Large Instance ou VM na rede virtual, que tem acesso ao ambiente HANA Large Instance e será usada para definir a palavra-passe.

   ```bash
   curl --cert snapcenter.pem --key snapcenter.key -X POST -k "https://10.0.40.11/api/security/authentication/password" -d '{"name":"snapcenter","password":"test1234"}'
   ```

   Certifique-se de que não existe uma variável proxy ativa no sistema HANA DB.

   ```bash
   sollabsjct31:/tmp # unset http_proxy
   sollabsjct31:/tmp # unset https_proxy
   ```

### <a name="download-and-install-snapcenter"></a>Descarregue e instale o SnapCenter
Agora que o nome de utilizador está configurado para o acesso do SnapCenter ao sistema de armazenamento, utilizará o nome de utilizador do SnapCenter para configurar o SnapCenter uma vez instalado. 

Antes de instalar o SnapCenter, [reveja a Cópia de Segurança/Recuperação DO SAP HANA com o SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf) para definir a sua estratégia de backup. 

1. Inscreva-se no [NetApp](https://mysupport.netapp.com) para [descarregar](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fmysupport.netapp.com%2Fsite%2Fproducts%2Fall%2Fdetails%2Fsnapcenter%2Fdownloads-tab&data=02%7C01%7Cmadhukan%40microsoft.com%7Ca53f5e2f245a4e36933008d816efbb54%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637284566603265503&sdata=TOANWNYoAr1q5z1opu70%2FUDPHjluvovqR9AKplYpcpk%3D&reserved=0) a versão mais recente do SnapCenter.

1. Instale o SnapCenter no Windows Azure VM.

   O instalador verifica os pré-requisitos do VM. 

   >[!IMPORTANT]
   >Preste atenção ao tamanho do VM, especialmente em ambientes maiores.

1. Configure as credenciais de utilizador para o SnapCenter. Por predefinição, preenche as credenciais de utilizador do Windows utilizadas para instalar a aplicação. 

   :::image type="content" source="media/snapcenter/installation-user-inputs-dialog.png" alt-text="Diálogo de entradas de utilizador de instalação"::: 

1. Quando iniciar a sessão, guarde a isenção de segurança e o GUI começa.

1. Inscreva-se no SnapCenter no VM https://snapcenter-vm:8146) (utilizando as credenciais do Windows para configurar o ambiente.


### <a name="set-up-the-storage-system"></a>Configurar o sistema de armazenamento

1. No SnapCenter, selecione **o Sistema de Armazenamento** e, em seguida, selecione **+New**. 

   :::image type="content" source="./media/snapcenter/snapcenter-storage-connections-window.png" alt-text="Conexões de armazenamento SnapCenter" lightbox="./media/snapcenter/snapcenter-storage-connections-window.png":::

   O padrão é um SVM por inquilino. Se um cliente tiver vários inquilinos ou HLIs em várias regiões, a recomendação é configurar todos os SVMs no SnapCenter

1. No Sistema de Armazenamento de Adicionar, forneça as informações para o Sistema de Armazenamento que pretende adicionar, o nome de utilizador do SnapCenter e a palavra-passe e, em seguida, selecione **Enviar por isso**.

   :::image type="content" source="./media/snapcenter/new-storage-connection.png" alt-text="Nova ligação de armazenamento":::

   >[!NOTE]
   >O padrão é um SVM por inquilino.  Se houver vários inquilinos, então a recomendação é configurar todos os SVMs aqui no SnapCenter. 

1. No SnapCenter, selecione **Hosts** e o select **+Add** para configurar o plug-in HANA e os anfitriões HANA DB.  A versão mais recente do SnapCenter deteta automaticamente a base de dados HANA no hospedeiro.

   :::image type="content" source="media/snapcenter/managed-hosts-new-host.png" alt-text="No SnapCenter, selecione Hosts e, em seguida, selecione Add." lightbox="media/snapcenter/managed-hosts-new-host.png":::

1. Forneça as informações para o novo anfitrião:
   1. Selecione o sistema operativo para o tipo de anfitrião.
   1. Introduza o nome de anfitrião SnapCenter VM.
   1. Forneça as credenciais que pretende utilizar.
   1. Selecione as opções **Microsoft Windows** e **SAP HANA** e, em seguida, selecione Enviar por isso **.**

   :::image type="content" source="media/snapcenter/add-new-host-operating-system-credentials.png" alt-text="Informação para novo hospedeiro":::

   >[!IMPORTANT]
   >Antes de poder instalar o primeiro nó, o SnapCenter permite que um utilizador sem raízes instale plug-ins na base de dados.  Para obter informações sobre como ativar um utilizador não-raiz, consulte [adicionar um utilizador sem raízes e configurar privilégios de sudo](https://library.netapp.com/ecmdocs/ECMLP2590889/html/GUID-A3EEB5FC-242B-4C2C-B407-510E48A8F131.html).

1. Reveja os detalhes do anfitrião e **selecione Enviar** por ela para instalar o plug-in no servidor Do SnapCenter.

1. Depois de instalar o plug-in, no SnapCenter, selecione **Hosts** e, em seguida, **selecione +Adicionar** para adicionar um nó HANA.

   :::image type="content" source="media/snapcenter/add-hana-node.png" alt-text="Adicione um nó HANA" lightbox="media/snapcenter/add-hana-node.png":::

1. Forneça as informações para o nó HANA:
   1. Selecione o sistema operativo para o tipo de anfitrião.
   1. Insira o nome de anfitrião HANA DB ou endereço IP.
   1. Selecione **+** para adicionar as credenciais configuradas no sistema operativo do anfitrião HANA DB e, em seguida, selecione **OK**.
   1. Selecione **SAP HANA** e, em seguida, selecione **Enviar por isso.**

   :::image type="content" source="media/snapcenter/add-hana-node-details.png" alt-text="Detalhes do nó SAP HANA":::

1. Confirme a impressão digital e **selecione Confirmar e Enviar**.

   :::image type="content" source="media/snapcenter/confirm-submit-fingerprint.png" alt-text="Confirmar e submeter impressões digitais":::

1. No nó HANA, na base de dados do sistema, selecione **O**  >  SNAPCENTER **dos Utilizadores** de Segurança para criar o utilizador Do  >  **SNAPCENTER** SnapCenter.

   :::image type="content" source="media/snapcenter/create-snapcenter-user-hana-system-db.png" alt-text="Criar o utilizador SnapCenter em HANA (sistema db)":::



### <a name="auto-discovery"></a>Descoberta de automóveis
O SnapCenter 4.3 permite a função de descoberta automática por predefinição.  A descoberta automática não é suportada para casos de HANA com replicação do sistema HANA (HSR) configurada. Deve adicionar manualmente a instância ao servidor Do SnapCenter.


### <a name="hana-setup-manual"></a>Configuração HANA (Manual)
Se configurar o HSR, deve configurar o sistema manualmente.  

1. No SnapCenter, selecione **Recursos** e **SAN HANA** (no topo) e, em seguida, selecione **+Add SAP HANA Database** (à direita).

   :::image type="content" source="media/snapcenter/manual-hana-setup.png" alt-text="Configuração manual hana" lightbox="media/snapcenter/manual-hana-setup.png":::

1. Especifique os detalhes de recursos do utilizador do administrador HANA configurados no anfitrião Linux ou no anfitrião onde os plug-ins estão instalados. A cópia de segurança será gerida a partir do plug-in no sistema Linux.

   :::image type="content" source="media/snapcenter/provide-resource-details-sap-hana-database.png" alt-text="Especifique os detalhes de recursos do utilizador administrador HANA configurados no anfitrião Linux.":::

1. Selecione o volume de dados para o qual necessita para tirar fotografias, **selecione Guardar** e, em seguida, selecione **Terminar**.

   :::image type="content" source="media/snapcenter/provide-storage-footprint.png" alt-text="Selecione o volume de dados para o qual necessita para tirar fotografias, selecione Guardar e, em seguida, selecione Terminar.":::

### <a name="create-a-snapshot-policy"></a>Criar uma política instantânea

Antes de utilizar o SnapCenter para fazer backup dos recursos da base de dados SAP HANA, tem de criar uma política de backup para o grupo de recursos ou recursos que pretende fazer backup. Durante o processo de criação de uma política de instantâneo, será-lhe dada a opção de configurar comandos pré/post e chaves SSL especiais. Para obter informações sobre como criar uma política de instantâneo, consulte [criar políticas de backup para bases de dados SAP HANA](http://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-sap-hana%2FGUID-246C0810-4F0B-4BF7-9A35-B729AD69954A.html).

1. No SnapCenter, selecione **Recursos** e, em seguida, selecione uma base de dados.

   :::image type="content" source="media/snapcenter/select-database-create-policy.png" alt-text="No SnapCenter, selecione Recursos e, em seguida, selecione uma base de dados.":::

1. Siga o fluxo de trabalho do assistente de configuração para configurar o programador de instantâneos.

   :::image type="content" source="media/snapcenter/follow-workflow-configuration-wizard.png" alt-text="Siga o fluxo de trabalho do assistente de configuração para configurar o programador de instantâneos." lightbox="media/snapcenter/follow-workflow-configuration-wizard.png":::

1. Forneça as opções para configurar comandos pré/post e teclas SSL especiais.  Neste exemplo, não estamos a usar configurações especiais.

   :::image type="content" source="media/snapcenter/configuration-options-pre-post-commands.png" alt-text="Forneça as opções para configurar comandos pré-post e teclas SSL especiais." lightbox="media/snapcenter/configuration-options-pre-post-commands.png":::

1. **Selecione Adicionar** para criar uma política de instantâneo, que também pode ser usada para outras bases de dados HANA. 

   :::image type="content" source="media/snapcenter/select-one-or-more-policies.png" alt-text="Selecione Adicionar para criar uma política de instantâneo, que também pode ser usada para outras bases de dados HANA.":::

1. Insira o nome da apólice e uma descrição.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy.png" alt-text="Insira o nome da apólice e uma descrição.":::


1. Selecione o tipo de cópia de segurança e a frequência.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-settings.png" alt-text="Selecione o tipo de cópia de segurança e a frequência.":::

1. Configure as **definições de retenção de backup on demand**.  No nosso exemplo, estamos a definir a retenção em três cópias instantâneas para guardar.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-retention-settings.png" alt-text="Configurar as definições de retenção de backup on demand.":::

1. Configure as **definições de retenção horária**. 

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-hourly-retention-settings.png" alt-text="Configure as definições de retenção horária.":::

1. Se uma configuração do SnapMirror estiver configurada, selecione **Update SnapMirror depois de criar uma cópia Local Do SnapShot**.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-snapmirror.png" alt-text="Se for necessário um SnapMirror, selecione Update SnapMirror depois de criar uma cópia snapshot local.":::

1. **Selecione Terminar** para rever o resumo da nova política de backup. 
1. Em **Configure Schedule**, selecione **Add**.

   :::image type="content" source="media/snapcenter/configure-schedules-for-selected-policies.png" alt-text="Em Configure Schedule, selecione Add.":::

1. Selecione a **data de início,** **expira na** data e na frequência.

   :::image type="content" source="media/snapcenter/add-schedules-for-policy.png" alt-text="Selecione a data de início, expira na data e na frequência.":::

1. Forneça os detalhes do e-mail para notificações.

   :::image type="content" source="media/snapcenter/backup-policy-notification-settings.png" alt-text="Forneça os detalhes do e-mail para notificações.":::

1.  **Selecione Acabamento** para criar a política de backup.

### <a name="disable-ems-message-to-netapp-autosupport"></a>Desative a mensagem EMS para o NetApp Autosupport
Por predefinição, a recolha de dados EMS está ativada e é executado de sete em sete dias após a data de instalação.  Pode desativar a recolha de dados com o cmdlet PowerShell `Disable-SmDataCollectionEms` .

1. Em PowerShell, estabeleça uma sessão com o SnapCenter.

   ```powershell
   Open-SmConnection
   ```

1. Inscreva-se com as suas credenciais.
1. Desative a recolha de mensagens EMS.

   ```powershell
   Disable-SmCollectionEms
   ```

### <a name="restore-database-after-crash"></a>Restaurar base de dados após acidente
Pode utilizar o SnapCenter para restaurar a base de dados.  Nesta secção, vamos cobrir os passos de alto nível, mas para mais informações, consulte [SAP HANA Backup/Recovery with SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf).


1. Pare a base de dados e elimine todos os ficheiros da base de dados.

   ```
   su - h31adm
   > sapcontrol -nr 00 -function StopSystem
   StopSystem
   OK
   > sapcontrol -nr 00 -function GetProcessList
   OK
   name, description, dispstatus, textstatus, starttime, elapsedtime, pid
   hdbdaemon, HDB Daemon, GRAY, Stopped, , , 35902
 
   ```

1. Desmonte o volume da base de dados.

   ```bash
   unmount /hana/data/H31/mnt00001
   ```


1. Restaurar os ficheiros de base de dados através do SnapCenter.  Selecione a base de dados e, em seguida, **selecione Restaurar**.  

   :::image type="content" source="media/snapcenter/restore-database-via-snapcenter.png" alt-text="Selecione uma base de dados e selecione Restaurar." lightbox="media/snapcenter/restore-database-via-snapcenter.png":::

1. Selecione o tipo de restauro.  No nosso exemplo, estamos a restaurar o recurso completo. 

   :::image type="content" source="media/snapcenter/restore-database-select-restore-type.png" alt-text="Selecione o tipo de restauro.":::

   >[!NOTE]
   >Com uma configuração padrão, não precisa de especificar comandos para fazer uma restauração local a partir do instantâneo do disco. 

   >[!TIP]
   >Se pretender restaurar um LUN específico dentro do volume, selecione **Nível de Ficheiro**.

1. Siga o fluxo de trabalho através do assistente de configuração.
   
   O SnapCenter restaura os dados para a localização original para que possa iniciar o processo de restauro em HANA. Além disso, uma vez que o SnapCenter não é capaz de modificar o catálogo de cópias de segurança (a base de dados está em baixo), é apresentado um aviso.

   :::image type="content" source="media/snapcenter/restore-database-job-details-warning.png" alt-text="Uma vez que o SnapCenter não é capaz de modificar o catálogo de cópias de segurança, é apresentado um aviso. ":::

1. Uma vez que todos os ficheiros da base de dados são restaurados, inicie o processo de restauro em HANA. No Estúdio HANA, em **Sistemas,** clique com o botão direito na base de dados do sistema e selecione **Backup and Recovery** Recover System  >  **Database**.

   :::image type="content" source="media/snapcenter/hana-studio-backup-recovery.png" alt-text="Inicie o processo de restauro em HANA.":::

1. Selecione um tipo de recuperação.

   :::image type="content" source="media/snapcenter/restore-database-select-recovery-type.png" alt-text="Selecione o tipo de recuperação.":::

1. Selecione a localização do catálogo de cópias de segurança.

   :::image type="content" source="media/snapcenter/restore-database-select-location-backup-catalog.png" alt-text="Selecione a localização do catálogo de cópias de segurança.":::

1. Selecione uma cópia de segurança para recuperar a base de dados SAP HANA.

   :::image type="content" source="media/snapcenter/restore-database-select-backup.png" alt-text="Selecione uma cópia de segurança para recuperar a base de dados SAP HANA.":::

   Uma vez recuperada a base de dados, aparece uma mensagem com um carimbo **recuperado para o tempo** e recuperado para registar **o** carimbo.

1. Em **Sistemas,** clique com o botão direito na base de dados do sistema e selecione **Backup and Recovery** Recover Recover Tenant  >  **Database**.
1. Siga o fluxo de trabalho do assistente para completar a recuperação da base de dados do inquilino. 

Para obter mais informações sobre a restauração de uma base de dados, consulte [a Cópia de Segurança/Recuperação do SAP HANA com o SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf).


### <a name="non-database-backups"></a>Backups não-base de dados
Pode restaurar volumes não-dados, por exemplo, uma partilha de ficheiros de rede (/hana/shared) ou uma cópia de segurança do sistema operativo.  Para obter mais informações sobre a restauração de um volume de não dados, consulte [a Cópia de Segurança/Recuperação do SAP HANA com o SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf).

### <a name="sap-hana-system-cloning"></a>Clonagem do sistema SAP HANA

Antes de conseguir clonar, deve ter a mesma versão HANA instalada como base de dados de origem. O SID e a identificação podem ser diferentes. 

:::image type="content" source="media/snapcenter/system-cloning-diagram.png" alt-text="Clonagem do sistema SAP HANA" lightbox="media/snapcenter/system-cloning-diagram.png" border="false":::

1. Crie uma loja de utilizadores de bases de dados HANA para a base de dados H34 a partir de /usr/sap/H34/HDB40.

   ```
   hdbuserstore set H34KEY sollabsjct34:34013 system manager
   ```
 
1. Desative a firewall.

   ```bash
   systemctl disable SuSEfirewall2
   systemctl stop  SuSEfirewall2
   ```

1. Instale o Java SDK.

   ```bash
   zypper in java-1_8_0-openjdk
   ```

1. No SnapCenter, adicione o anfitrião de destino no qual o clone será montado. Para obter mais informações, consulte [adicionar anfitriões e instalar pacotes plug-in em anfitriões remotos.](http://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-sap-hana%2FGUID-246C0810-4F0B-4BF7-9A35-B729AD69954A.html)
   1. Forneça as informações para a execução como credenciais que pretende adicionar. 
   1. Selecione o sistema operativo hospedeiro e introduza as informações do anfitrião.
   1. Em **Plug-ins para instalar,** selecione a versão, introduza o caminho de instalação e selecione **SAP HANA**.
   1. **Selecione Validate** para executar as verificações de pré-instalação.

1. Pare a HANA e desmonte o antigo volume de dados.  Vai montar o clone do SnapCenter.  

   ```bash
   sapcontrol -nr 40 -function StopSystem
   umount /hana/data/H34/mnt00001

   ```
 1. Crie os ficheiros de script de configuração e de descodão para o alvo.
 
    ```bash
    mkdir /NetApp
    chmod 777 /NetApp
    cd NetApp
    chmod 777 sc-system-refresh-H34.cfg
    chmod 777 sc-system-refresh.sh

    ```

    >[!TIP]
    >Pode copiar os scripts da [CLONAGEM SAP do SnapCenter](https://www.netapp.com/us/media/tr-4667.pdf).

1. Modificar o ficheiro de configuração. 

   ```bash
   vi sc-system-refresh-H34.cfg
   ```

   * HANA_ARCHITECTURE="MDC_single_tenant"
   * KEY="H34KEY"
   * TIME_OUT_START=18
   * TIME_OUT_STOP=18
   * INSTANCENO="40"
   * ARMAZENAMENTO="10.250.101.33"

1. Modifique o ficheiro de script da concha.

   ```bash
   vi sc-system-refresh.sh
   ```  

   * VERBOSE=NO
   * MY_NAME=" `basename $0` "
   * BASE_SCRIPT_DIR=" `dirname $0` "
   * MOUNT_OPTIONS="rw,vers=4,hard,timeo=600,rsize=1048576,wsize=1048576,intr,noatime,nolock"

1. Inicie o clone a partir de um processo de reserva. Selecione o anfitrião para criar o clone. 

   >[!NOTE]
   >Para mais informações, consulte [Cloning a partir de uma cópia de segurança.](https://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-cpi%2FGUID-F6E7FF73-0183-4B9F-8156-8D7DA17A8555.html)

1. Nos **Scripts,** fornecer o seguinte:

   * **Comando de montagem:** /NetApp/sc-system-refresh.sh montagem H34 %hana_data_h31_mnt00001_t250_vol_Clone
   * **Comando de clone pós-clone:** /NetApp/sc-system-refresh.sh recuperar H34

1. Desative (bloqueio) o suporte automático no /etc/fstab uma vez que o volume de dados da base de dados pré-instalada não é necessário. 

   ```bash
   vi /etc/fstab
   ```

### <a name="delete-a-clone"></a>Apagar um clone

Pode apagar um clone se já não for necessário. Para mais informações, consulte [a Eliminação de Clones.](https://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-cpi%2FGUID-F6E7FF73-0183-4B9F-8156-8D7DA17A8555.html)

Os comandos usados para executar antes da eliminação do clone, são:
* **Pré-clone eliminar:** /NetApp/sc-system-refresh.sh desligar H34
* **Desmontagem:** /NetApp/sc-system-refresh.sh umount H34

Estes comandos permitem ao SnapCenter interrom liderar a base de dados, desmontar o volume e eliminar a entrada fstab.  Depois disso, o FlexClone é eliminado. 

### <a name="cloning-database-logfile"></a>Ficheiro de registo de bases de dados de clonagem

```   
20190502025323###sollabsjct34###sc-system-refresh.sh: Adding entry in /etc/fstab.
20190502025323###sollabsjct34###sc-system-refresh.sh: 10.250.101.31:/Sc21186309-ee57-41a3-8584-8210297f791d /hana/data/H34/mnt00001 nfs rw,vers=4,hard,timeo=600,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
20190502025323###sollabsjct34###sc-system-refresh.sh: Mounting data volume.
20190502025323###sollabsjct34###sc-system-refresh.sh: mount /hana/data/H34/mnt00001
20190502025323###sollabsjct34###sc-system-refresh.sh: Data volume mounted successfully.
20190502025323###sollabsjct34###sc-system-refresh.sh: chown -R h34adm:sapsys /hana/data/H34/mnt00001
20190502025333###sollabsjct34###sc-system-refresh.sh: Recover system database.
20190502025333###sollabsjct34###sc-system-refresh.sh: /usr/sap/H34/HDB40/exe/Python/bin/python /usr/sap/H34/HDB40/exe/python_support/recoverSys.py --command "RECOVER DATA USING SNAPSHOT CLEAR LOG"
[140278542735104, 0.005] >> starting recoverSys (at Thu May  2 02:53:33 2019)
[140278542735104, 0.005] args: ()
[140278542735104, 0.005] keys: {'command': 'RECOVER DATA USING SNAPSHOT CLEAR LOG'}
recoverSys started: ============2019-05-02 02:53:33 ============
testing master: sollabsjct34
sollabsjct34 is master
shutdown database, timeout is 120
stop system
stop system: sollabsjct34
stopping system: 2019-05-02 02:53:33
stopped system: 2019-05-02 02:53:33
creating file recoverInstance.sql
restart database
restart master nameserver: 2019-05-02 02:53:38
start system: sollabsjct34
2019-05-02T02:53:59-07:00  P010976      16a77f6c8a2 INFO    RECOVERY state of service: nameserver, sollabsjct34:34001, volume: 1, RecoveryPrepared
recoverSys finished successfully: 2019-05-02 02:54:00
[140278542735104, 26.490] 0
[140278542735104, 26.490] << ending recoverSys, rc = 0 (RC_TEST_OK), after 26.485 secs
20190502025400###sollabsjct34###sc-system-refresh.sh: Wait until SAP HANA database is started ....
20190502025400###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025410###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025420###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025430###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025440###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025451###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502025451###sollabsjct34###sc-system-refresh.sh: SAP HANA database is started.
20190502025451###sollabsjct34###sc-system-refresh.sh: Recover tenant database H34.
20190502025451###sollabsjct34###sc-system-refresh.sh: /usr/sap/H34/SYS/exe/hdb/hdbsql -U H34KEY RECOVER DATA FOR H34 USING SNAPSHOT CLEAR LOG
0 rows affected (overall time 69.584135 sec; server time 69.582835 sec)
20190502025600###sollabsjct34###sc-system-refresh.sh: Checking availability of Indexserver for tenant H34.
20190502025601###sollabsjct34###sc-system-refresh.sh: Recovery of tenant database H34 succesfully finished.
20190502025601###sollabsjct34###sc-system-refresh.sh: Status: GREEN
Deleting the DB Clone – Logfile
20190502030312###sollabsjct34###sc-system-refresh.sh: Stopping HANA database.
20190502030312###sollabsjct34###sc-system-refresh.sh: sapcontrol -nr 40 -function StopSystem HDB

02.05.2019 03:03:12
StopSystem
OK
20190502030312###sollabsjct34###sc-system-refresh.sh: Wait until SAP HANA database is stopped ....
20190502030312###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030322###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030332###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030342###sollabsjct34###sc-system-refresh.sh: Status:  GRAY
20190502030342###sollabsjct34###sc-system-refresh.sh: SAP HANA database is stopped.
20190502030347###sollabsjct34###sc-system-refresh.sh: Unmounting data volume.
20190502030347###sollabsjct34###sc-system-refresh.sh: Junction path: Sc21186309-ee57-41a3-8584-8210297f791d
20190502030347###sollabsjct34###sc-system-refresh.sh: umount /hana/data/H34/mnt00001
20190502030347###sollabsjct34###sc-system-refresh.sh: Deleting /etc/fstab entry.
20190502030347###sollabsjct34###sc-system-refresh.sh: Data volume unmounted successfully.

```

### <a name="uninstall-snapcenter-plug-ins-package-for-linux"></a>Desinstalar pacote de plug-ins do SnapCenter para o Linux

Pode desinstalar o pacote de plug-ins Linux a partir da linha de comando. Como a implementação automática espera um novo sistema, é fácil desinstalar o plug-in.  

>[!NOTE]
>Pode ser necessário desinstalar manualmente uma versão mais antiga do plug-in. 

Desinstale os plug-ins.

```bash
cd /opt/NetApp/snapcenter/spl/installation/plugins
./uninstall
```

Agora pode instalar o mais recente plug-in HANA no novo nó selecionando **SUBMIT** no SnapCenter. 




## <a name="next-steps"></a>Passos seguintes
- Consulte [os princípios de recuperação de desastres e a preparação.](hana-concept-preparation.md)
