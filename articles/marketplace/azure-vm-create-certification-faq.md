---
title: Resolução de problemas de certificação da máquina virtual (VM) para o Azure Marketplace
description: Resolver problemas comuns relacionados com testes e certificação de imagens de máquinas virtuais (VM) para o Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: mathapli
ms.author: mathapli
ms.date: 01/18/2021
ms.openlocfilehash: 7a9fa4a6f44e88caa6f98d5e1c56adaa065fa29a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558714"
---
# <a name="troubleshoot-virtual-machine-certification"></a>Certificação de máquina virtual de resolução de problemas

Ao publicar a sua imagem de máquina virtual (VM) no Azure Marketplace, a equipa do Azure valida-a para garantir que é bootable, segura e compatível com o Azure. Se a sua imagem em VM falhar em nenhum dos testes de alta qualidade, não será publicado. Receberá uma mensagem de erro que descreve o problema.

Este artigo explica mensagens de erro comuns durante a publicação de imagens VM, juntamente com soluções relacionadas.

> [!NOTE]
> Se tiver dúvidas sobre este artigo ou sugestões de melhoria, contacte [o suporte do Partner Center](https://aka.ms/marketplacepublishersupport).

## <a name="vm-extension-failure"></a>Falha de extensão VM

Verifique se a sua imagem suporta extensões VM.

Para ativar extensões VM:

1. Selecione o seu Linux VM.
1. Ir para as **definições de Diagnóstico**.
1. Ativar as matrizes de base atualizando a **conta de Armazenamento.**
1. Selecione **Guardar**.

   ![Screenshot que mostra como ativar a monitorização ao nível do hóspede.](./media/create-vm/vm-certification-issues-solutions-1.png)

Para verificar se as extensões VM estão corretamente ativadas:

1. No VM, selecione o separador **de extensões VM** e, em seguida, verifique o estado da extensão de **diagnóstico linux**.
1. Verifique o estado de provisionamento.

   - Se o estado for *Provisioning Succeeded,* o caso do teste de extensões passou.  
   - Se o estado for *Provisioning Failed,* o caso de teste de extensões falhou e tem de definir a bandeira endurecida.

   ![Screenshot mostrando que o provisionamento foi bem sucedido.](./media/create-vm/vm-certification-issues-solutions-2.png)

   Se a extensão VM falhar, consulte [a extensão de diagnóstico do Linux para monitorizar métricas e registos](../virtual-machines/extensions/diagnostics-linux.md) para a ativar. Se não quiser ativar a extensão VM, contacte a equipa de Suporte e peça-lhes que a desativem.

## <a name="vm-provisioning-issue"></a>Emissão de provisionamento de VM

Verifique se seguiu rigorosamente o processo de provisionamento de VM antes de submeter a sua oferta. Para visualizar o formato JSON para o provisionamento do VM, consulte [testar uma imagem de máquina virtual](azure-vm-image-test.md).

As questões de provisionamento podem incluir os seguintes cenários de falha:

|Scenario|Erro|Razão|Solução|
|---|---|---|---|
|1|Disco rígido virtual inválido (VHD)|Se o valor de cookie especificado no rodapé VHD estiver incorreto, o VHD será considerado inválido.|Re-crie a imagem e envie o pedido.|
|2|Tipo de bolha inválida|O fornecimento de VM falhou porque o bloco usado é um tipo de bolha em vez de um tipo de página.|Re-crie a imagem e envie o pedido.|
|3|Provisionamento do tempo limite ou não devidamente generalizado|Há um problema com a generalização da VM.|Re-crie a imagem com generalização e submeta o pedido.|
|

> [!NOTE]
> Para obter mais informações sobre a generalização da VM, consulte:
> - [Documentação linux](azure-vm-create-using-approved-base.md#generalize-the-image)
> - [Documentação do Windows](../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)

## <a name="vhd-specifications"></a>Especificações VHD

### <a name="conectix-cookie-and-other-vhd-specifications"></a>Cookie conectix e outras especificações de VHD

A cadeia 'conectix' faz parte da especificação VHD. É definido como o cookie de 8 byte no rodapé VHD que identifica o criador de ficheiros. Todos os ficheiros VHD criados pela Microsoft têm este cookie.

Uma bolha em formato VHD deve ter um rodapé de 512 byte neste formato:

|Campos de rodapé de disco rígido|Tamanho (bytes)|
|---|---|
Cookie|8
Funcionalidades|4
Versão em formato de ficheiro|4
Compensação de dados|8
Time Stamp (Carimbo de Data/Hora)|4
Aplicação do Criador|4
Versão do Criador|4
Criador Host OS|4
Tamanho Original|8
Tamanho atual|8
Geometria do disco|4
Tipo de Disco|4
Soma de verificação|4
ID Único|16
Estado Salvo|1
Reservado|427
|

### <a name="vhd-specifications"></a>Especificações VHD

Para garantir uma experiência de publicação suave, certifique-se de que o seu VHD cumpre os seguintes critérios:

- O cookie contém a corda 'conectix'.
- O tipo de disco é Fixo.
- O tamanho virtual do VHD é de pelo menos 20 MB.
- O VHD está alinhado. O tamanho virtual deve ser um múltiplo de 1 MB.
- O comprimento do blob VHD é igual ao tamanho virtual mais o comprimento do rodapé VHD (512).

Descarregue a [especificação VHD.](https://www.microsoft.com/download/details.aspx?id=23850)

## <a name="software-compliance-for-windows"></a>Conformidade de software para Windows

Se o seu pedido de imagem do Windows for rejeitado devido a um problema de conformidade com o software, poderá ter criado uma imagem do Windows com uma instância do SQL Server instalada. Em vez disso, precisa de tirar a imagem base da versão do SQL Server relevante do Azure Marketplace.

Não crie a sua própria imagem Windows com o SQL Server instalado na mesma. Utilize as imagens de base aprovadas do SQL Server (Enterprise/Standard/web) do Azure Marketplace.

Se estiver a tentar instalar o Visual Studio ou qualquer produto licenciado pelo Office, contacte a equipa de Suporte para obter aprovação prévia.

Para obter mais informações sobre a seleção de uma base aprovada, consulte [Criar uma máquina virtual a partir de uma base aprovada.](azure-vm-create-using-approved-base.md)

## <a name="toolkit-test-case-execution-failed"></a>Execução de caso de teste de toolkit falhou

O conjunto de ferramentas de certificação da Microsoft pode ajudá-lo a executar casos de teste e verificar se o seu VHD ou imagem é compatível com o ambiente Azure.

Descarregue o [kit de ferramentas da Microsoft Certification](azure-vm-image-test.md).

### <a name="linux-test-cases"></a>Casos de teste linux

A tabela que se segue lista os casos de teste Linux que o conjunto de ferramentas irá executar. A validação do teste está indicada na descrição.

|Scenario|Caso de teste|Descrição|
|---|---|---|
|1|História da bash|Os ficheiros de histórico de bash devem ser limpos antes de criar a imagem VM.|
|2|Versão linux agent|O Agente Azure Linux 2.2.41 ou mais tarde deve ser instalado.|
|3|Parâmetros de núcleo necessários|Verifica se os seguintes parâmetros do núcleo são definidos: <br>console=ttyS0<br>earlyprintk=ttyS0<br>rootdelay=300 |
|4|Trocar divisórias no disco DE|Verifica que as divisórias de troca não são criadas no disco de so.|
|5|Partição de raiz no disco de SO|Crie uma única divisória de raiz para o disco DE.|
|6|Versão OpenSSL|A versão OpenSSL deve ser v0.9.8 ou posterior.|
|7|Versão de Python|A versão python 2.6 ou mais tarde é altamente recomendada.|
|8|Intervalo de cliente vivo|Desafie o ClientAliveInterval a 180. Sobre a necessidade de aplicação, pode ser definido de 30 a 235. Se estiver a ativar o SSH para os seus utilizadores finais, este valor deve ser definido como explicado.|
|9|Arquitetura de SO|Apenas os sistemas operativos de 64 bits são suportados.|
|10|Atualização automática|Identifica se a atualização automática do agente Linux está ativada.|
|

### <a name="common-test-case-errors"></a>Erros comuns do caso de teste

Consulte a tabela seguinte para os erros comuns que pode ver ao executar casos de teste:

| Scenario | Caso de teste | Erro | Solução |
| --- | --- | --- | --- |
| 1 | Caso de teste de versão linux agente | A versão mínima do agente Linux é 2.2.41 ou mais tarde. Esta exigência é obrigatória desde 1 de maio de 2020. | Atualize a versão do agente Linux. Deve ser 2.241 ou mais tarde. Para mais informações, visite a [página de atualização da versão do agente Linux](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support). |
| 2 | Caso de teste de história de bash | Ocorre um erro se o tamanho da história da Bash na sua imagem submetida for superior a 1 quilobyte (KB). O tamanho é restrito a 1 KB para garantir que o seu ficheiro de histórico bash não contém nenhuma informação potencialmente sensível. | Resolva montando o VHD em outro VM em funcionamento e faça alterações para reduzir o tamanho para 1 KB ou menos. Por exemplo, elimine os `.bash` ficheiros de histórico. |
| 3 | Caso de teste do parâmetro do núcleo necessário | Receberá este erro quando o valor `console` não estiver definido para `ttyS0` . Verifique executando o seguinte comando: <br /> `cat /proc/cmdline` | Descreva o valor `console` para `ttyS0` , e reenviar o pedido. |
| 4 | Caso de teste de intervalo clientealive | Se o conjunto de ferramentas lhe der um resultado falhado para este caso de teste, há um valor inadequado para `ClientAliveInterval` . | Descreva o valor `ClientAliveInterval` para menos ou igual a 235 e, em seguida, reenvia o pedido. |
|

### <a name="windows-test-cases"></a>Casos de teste do Windows

A tabela que se segue lista os casos de teste do Windows que o conjunto de ferramentas irá executar, juntamente com uma descrição da validação do teste:

|Scenario |Casos de teste|Descrição|
|---|---|---|
|1|Arquitetura de SO|O Azure suporta apenas sistemas operativos de 64 bits.|
|2|Dependência da conta do utilizador|A execução da aplicação não deve depender da conta do administrador.|
|3|Cluster de ativação pós-falha|A funcionalidade de clustering de falha do Windows Server ainda não está suportada. A aplicação não deve depender desta funcionalidade.|
|4|IPV6|O IPv6 ainda não é apoiado no ambiente Azure. A aplicação não deve depender desta funcionalidade.|
|5|DHCP|O papel do Servidor do Protocolo de Configuração do Anfitrião Dinâmico ainda não está suportado. A aplicação não deve depender desta funcionalidade.|
|6|Hyper-V|O papel do Servidor Hiper-V ainda não está suportado. A aplicação não deve depender desta funcionalidade.|
|7|Acesso remoto|A função do servidor acesso remoto (Acesso Direto) ainda não está suportada. A aplicação não deve depender desta funcionalidade.|
|8|Serviços de Gestão de Direitos|Serviços de Gestão de Direitos. O papel do servidor ainda não está suportado. A aplicação não deve depender desta funcionalidade.|
|9|Windows Deployment Services|Serviços de implantação do Windows. O papel do servidor ainda não está suportado. A aplicação não deve depender desta funcionalidade.|
|10|Encriptação de Unidade BitLocker|A encriptação BitLocker Drive não é suportada no disco rígido do sistema operativo, mas pode ser usada em discos de dados.|
|11|Servidor de nome de armazenamento de internet|A funcionalidade do Servidor de Nome de Armazenamento de Internet ainda não está suportada. A aplicação não deve depender desta funcionalidade.|
|12|Multipath I/O|I/O multipata. Esta funcionalidade do servidor ainda não está suportada. A aplicação não deve depender desta funcionalidade.|
|13|Balanceamento de Carga na Rede|Equilíbrio de carga de rede. Esta funcionalidade do servidor ainda não está suportada. A aplicação não deve depender desta funcionalidade.|
|14|Protocolo PNRP (Peer Name Resolution Protocol)|Protocolo de Resolução de Nome de Pares. Esta funcionalidade do servidor ainda não está suportada. A aplicação não deve depender desta funcionalidade.|
|15|Serviços SNMP|A funcionalidade de Serviços de Gestão de Redes Simples (SNMP) ainda não está suportada. A aplicação não deve depender desta funcionalidade.|
|16|Serviço de Nome da Internet do Windows|Serviço de Nome da Internet do Windows. Esta funcionalidade do servidor ainda não está suportada. A aplicação não deve depender desta funcionalidade.|
|17|Serviço LAN Sem Fios|Serviço LAN sem fios. Esta funcionalidade do servidor ainda não está suportada. A aplicação não deve depender desta funcionalidade.|
|

Se encontrar falhas com os casos de teste anteriores, consulte a coluna **Descrição** na tabela para obter a solução. Para mais informações, contacte a equipa de Apoio.

## <a name="data-disk-size-verification"></a>Verificação do tamanho do disco de dados

Os pedidos de disco de dados com um tamanho superior a 1023 gigabytes (GB) não serão aprovados. Esta regra aplica-se tanto ao Linux como ao Windows.

Reenviar o pedido com um tamanho inferior ou igual a 1023 GB.

## <a name="os-disk-size-validation"></a>Validação do tamanho do disco de OS

Consulte as seguintes regras para limitações no tamanho do disco de SO. Quando submeter qualquer pedido, verifique se o tamanho do disco OS está dentro da limitação para Linux ou Windows.

|SO|Tamanho VHD recomendado|
|---|---|
|Linux|1 GB a 1023 GB|
|Windows|30 GB a 250 GB|
|

Uma vez que os VMs permitem o acesso ao sistema operativo subjacente, certifique-se de que o tamanho VHD é suficientemente grande para o VHD. Os discos não são expansíveis sem tempo de inatividade. Utilize um tamanho de disco de 30 GB a 50 GB.

|Tamanho VHD|Tamanho real ocupado|Solução|
|---|---|---|
|>500 tebibytes (TiB)|n/a|Contacte a equipa de Apoio para obter uma aprovação de exceção.|
|250-500 TiB|>diferença de 200 gibibytes (GiB) do tamanho do blob|Contacte a equipa de Apoio para obter uma aprovação de exceção.|
|

> [!NOTE]
> Os tamanhos maiores do disco incorrem em custos mais elevados e resultarão num atraso durante o processo de configuração e replicação. Devido a este atraso e custo, a equipa de Apoio poderá procurar justificação para a aprovação da exceção.

## <a name="wannacry-patch-verification-test-for-windows"></a>Teste de verificação de patch do WannaCry para Windows

Para evitar um possível ataque relacionado com o vírus WannaCry, certifique-se de que todos os pedidos de imagem do Windows são atualizados com o mais recente patch.

Pode verificar a versão do ficheiro de imagem de `C:\windows\system32\drivers\srv.sys` ou `srv2.sys` .

A tabela a seguir mostra a versão mínima remendada do Windows Server:

|SO|Versão|
|---|---|
|Windows Serve 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|ND|
|

> [!NOTE]
> O Windows Server 2019 não tem quaisquer requisitos de versão obrigatória.

## <a name="sack-vulnerability-patch-verification"></a>Verificação de patch de vulnerabilidade SACK

Quando submete uma imagem linux, o seu pedido pode ser rejeitado devido a problemas de versão kernel.

Atualize o núcleo com uma versão aprovada e reenvia o pedido. Pode encontrar a versão de kernel aprovada na tabela seguinte. O número da versão deve ser igual ou superior ao número aqui indicado.

Se a sua imagem não estiver instalada com uma das seguintes versões de kernel, atualize-a com as correções corretas. Solicite a aprovação necessária da equipa de Suporte após a atualização da imagem com estes patches necessários:

- CVE-2019-11477
- CVE-2019-11478
- CVE-2019-11479

|Família osa|Versão|Kernel|
|---|---|---|
|Ubuntu|14.04 LTS|4.4.0-151| 
||14.04 LTS|4.15.0-1049- \* Azure|
||16.04 LTS|4.15.0-1049|
||18.04 LTS|4.18.0-1023|
||18.04 LTS|5.0.0-1025|
||18.10|4.18.0-1023|
||19.04|5.0.0-1010|
||19.04|5.3.0-1004|
|RHEL e Cent OS|6.10|2.6.32-754.15.3|
||7.2|3.10.0-327.79.2|
||7.3|3.10.0-514.66.2|
||7.4|3.10.0-693.50.3|
||7,5|3.10.0-862.34.2|
||7.6|3.10.0-957.21.3|
||7.7|3.10.0-1062.1.1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0-147|
||"7-RAW" (7.6)||
||"7-LVM" (7.6)|3.10.0-957.21.3|
||RHEL-SAP 7.4|TBD|
||RHEL-SAP 7.5|TBD|
|SLES|SLES11SP4 (incluindo SAP)|3.0.101-108.95.2|
||SLES12SP1 para SAP|3.12.74-60.64.115.1|
||SLES12SP2 para SAP|4.4.121-92.114.1|
||SLES12SP3|4.4180-4.31.1 (kernel-azure)|
||SLES12SP3 para SAP|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 (kernel-azure)|
||SLES12SP4 para SAP|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 (kernel-azure)|
||SLES15 para SAP|4.12.14-5.30.1 (kernel-azure)|
||SLES15SP1|4.12.14-5.30.1 (kernel-azure)|
|Oracle|6.10|UEK2 2.6.39-400.312.2<br>UEK3 3.8.13-118.35.2<br>RISSA 2.6.32-754.15.3 
||7.0-7.5|UEK3 3.8.13-118.35.2<br>UEK4 4.1.12-124.28.3<br>RHCK segue RHEL acima|
||7.6|RISSA 3.10.0-957.21.3<br>UEK5 4.14.35-1902.2.0|
|CoreOS Estável 2079.6.0|4.19.43\*|
||Beta 2135.3.1|4.19.50\*|
||Alfa 2163.2.1|4.19.50\*|
|Debian|jessie (segurança)|3.16.68-2|
||jessie backports|4.9.168-1+deb9u3|
||alongamento (segurança)|4.9.168-1+deb9u3|
||Debian GNU/Linux 10 (buster)|Debian 6.3.0-18+deb9u1|
||buster, sid (extensões backports)|4.19.37-5|
|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>O tamanho da imagem deve estar em múltiplos de megabytes

Todos os VHDs em Azure devem ter um tamanho virtual alinhado a múltiplos de 1 megabyte (MB). Se o seu VHD não aderir ao tamanho virtual recomendado, o seu pedido poderá ser rejeitado.

Siga as diretrizes quando converter de um disco cru para VHD. Certifique-se de que o tamanho do disco bruto é um múltiplo de 1 MB. Para obter mais informações, consulte [informações para distribuição não endossada.](../virtual-machines/linux/create-upload-generic.md)

## <a name="vm-access-denied"></a>Acesso VM negado

Um _problema de acesso negado_ para executar um caso de teste no VM pode ser causado por privilégios insuficientes.

Verifique se permitiu o acesso adequado à conta em que os casos de auto-teste estão a decorrer. Permitir o acesso a executar casos de teste se não estiver ativado. Se não quiser ativar o acesso, poderá partilhar os resultados do caso de auto-teste com a equipa de Suporte.

Para submeter o seu pedido com imagem desativada SSH para o processo de certificação:

1. Execute a [mais recente Ferramenta de Teste de Certificação para VMs Azure](https://aka.ms/AzureCertificationTestTool) na sua imagem.

2. Levante um [bilhete de apoio.](https://aka.ms/marketplacepublishersupport) Certifique-se de anexar o relatório do conjunto de ferramentas e fornecer detalhes da oferta:
   - Nome da oferta
   - Nome do publicador
   - ID do plano/SKU e versão

3. Reenviar o seu pedido de certificação.

## <a name="download-failure"></a>Falha no download

Consulte a tabela seguinte para quaisquer problemas que surjam quando descarrega a imagem VM com um URL de assinatura de acesso partilhado (SAS).

|Erro|Razão|Solução|
|---|---|---|
|Blob não encontrado|O VHD pode ser eliminado ou deslocado do local especificado.|| 
|Bolha em uso|O VHD é utilizado por outro processo interno.|O VHD deve estar num estado usado quando o descarrega com um URL SAS.|
|URL DE SAS inválido|O URL SAS associado para o VHD está incorreto.|Obtenha o URL SAS correto.|
|Assinatura inválida|O URL SAS associado para o VHD está incorreto.|Obtenha o URL SAS correto.|
|Cabeçalho condicional HTTP|O URL SAS é inválido.|Obtenha o URL SAS correto.|
|Nome VHD inválido|Verifique se existem caracteres especiais, como um sinal por cento `%` ou aspas, `"` no nome VHD.|Mude o nome do ficheiro VHD removendo os caracteres especiais.|
|

## <a name="first-partition-starts-at-1-mb-2048-sectors"></a>A primeira partição começa em 1 MB (2048 Sectores)

Se estiver [a construir a sua própria imagem,](azure-vm-create-using-own-image.md)certifique-se de que os primeiros sectores de 2048 (1 MB) do disco DE ESestão vazios. Caso contrário, a sua publicação falhará. Este requisito é aplicável apenas ao disco de SO (não discos de dados). Se estiver a construir a sua imagem [a partir de uma base aprovada,](azure-vm-create-using-approved-base.md)pode ignorar este requisito.

### <a name="create-a-1-mb-2048-sectors-each-sector-of-512-bytes-partition-on-an-empty-vhd"></a>Criar uma partição de 1 MB (2048, cada sector de 512 bytes) numa VHD vazia

Estes passos aplicam-se apenas ao Linux.

1. Crie qualquer tipo de Linux VM, como Ubuntu, Cent OS, ou outro. Preencha os campos necessários e selecione **Seguinte: Discos >**.

   ![Screenshot que mostra a página de máquina virtual com o botão 'Seguinte: Comando de discos' realçado.](./media/create-vm/vm-certification-issues-solutions-15.png)

1. Crie um disco não gerido para o seu VM.

   Utilize os valores predefinidos ou especifique qualquer valor para campos como NIC, NSG e IP público.

   ![Imagem de screenshot da página 'Discos de dados' na Criação de um fluxo de máquina virtual.](./media/create-vm/vm-certification-issues-solutions-16.png)

1. Depois de criar o VM, selecione **Discos** no painel esquerdo.

   ![Screenshot mostrando como selecionar discos para um VM.](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Anexe o seu VHD como disco de dados ao seu VM para criar uma tabela de divisórias.

   1. **Selecione Adicionar DataDisk**  >  **Blob existente**.

      ![Screenshot mostrando como adicionar um disco de dados ao seu VHD.](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. Encontre a sua conta de armazenamento VHD.
   1. Selecione **o Recipiente** e, em seguida, selecione o seu VHD.
   1. Selecione **OK**.

      ![Screenshot da página de disco não gerido anexado.](./media/create-vm/vm-certification-issues-solutions-19.png)

      O seu VHD será adicionado como disco de dados LUN 0.

   1. Reinicie a VM.

1. Depois de reiniciar o VM, inicie sessão no VM utilizando o Putty ou outro cliente e execute o `sudo  -i` comando para obter acesso à raiz.

   ![Imagem de linha de comando do cliente putty mostrando o comando sudo-i.](./media/create-vm/vm-certification-issues-solutions-20.png)

1. Crie uma divisória no seu VHD.

   1. Entra no `fdisk /dev/sdb` comando.
   1. Para ver a lista de divisórias existente a partir do seu VHD, insira `p` .
   1. Introduza `d` para eliminar todas as divisórias existentes disponíveis no seu VHD. Pode saltar este passo, se não for necessário.

      ![Imagem de linha de comando do cliente putty mostrando os comandos para eliminar as divisórias existentes.](./media/create-vm/vm-certification-issues-solutions-21.png)

   1. Introduza `n` para criar uma nova partição e selecione para `p` (partição primária).

   1. Insira 2048 como primeiro valor _do setor._ Pode deixar _o último sector_ como o valor padrão.

      >[!IMPORTANT]
      >Os dados existentes serão apagados até 2048 (cada sector de 512 bytes). Cópia de segurança do VHD antes de criar uma nova divisória.

      ![Imagem de linha de comando do cliente putty mostrando os comandos e saída para dados apagados.](./media/create-vm/vm-certification-issues-solutions-22.png)

   1. Escreva `w` para confirmar a criação de partição.

      ![Imagem de linha de comando do cliente putty mostrando os comandos para criar uma divisória.](./media/create-vm/vm-certification-issues-solutions-23.png)

   1. Pode verificar a tabela de partição executando o comando `n fdisk /dev/sdb` e digitando `p` . Verá que a partição é criada com valor de compensação de 2048.

      ![Imagem de linha de comando do cliente putty mostrando os comandos para criar o offset de 2048.](./media/create-vm/vm-certification-issues-solutions-24.png)

1. Retire o VHD da VM e apague o VM.

### <a name="create-a-1-mb-2048-sectors-each-sector-of-512-bytes-partition-by-moving-existing-data-on-vhd"></a>Criar uma partição de 1 MB (2048, cada sector de 512 bytes) movendo os dados existentes sobre vHD

Estes passos aplicam-se apenas ao Linux.

1. Crie qualquer tipo de Linux VM, como Ubuntu, Cent OS, ou outro. Preencha os campos necessários e selecione **Seguinte: Discos >**.

   ![Screenshot que mostra a página de máquina virtual com o botão 'Seguinte: Comando de discos' realçado.](./media/create-vm/vm-certification-issues-solutions-15.png)

1. Crie um disco não gerido para o seu VM.

   ![Imagem de screenshot da página 'Discos de dados' na Criação de um fluxo de máquina virtual.](./media/create-vm/vm-certification-issues-solutions-16.png)

   Utilize os valores predefinidos ou especifique qualquer valor para campos como NIC, NSG e IP público.

1. Depois de criar o VM, selecione **Discos** no painel esquerdo.

   ![Screenshot mostrando como selecionar discos para um VM.](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Anexe o seu VHD como disco de dados ao seu VM para criar uma tabela de divisórias.

   1. Anexe o seu VHD como disco de dados ao seu VM para criar uma tabela de divisórias.

   1. **Selecione Adicionar DataDisk**  >  **Blob existente**.

      ![Screenshot mostrando como adicionar um disco de dados ao seu VHD.](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. Encontre a sua conta de armazenamento VHD.
   1. Selecione **o Recipiente** e, em seguida, selecione o seu VHD.
   1. Selecione **OK**.

      ![Screenshot da página de disco não gerido anexado.](./media/create-vm/vm-certification-issues-solutions-19.png)

      O seu VHD será adicionado como disco de dados LUN 0.

   1. Reinicie a VM.

1. Faça login no VM com Putty ou outro cliente e executar `sudo  -i` o comando para obter acesso à raiz.

   ![Imagem de linha de comando do cliente putty mostrando login e o comando sudo -i.](./media/create-vm/vm-certification-issues-solutions-20.png)

1. Execute o comando `echo '+1M,' | sfdisk --move-data /dev/sdc -N 1`.

   ![Imagem de linha de comando do cliente putty mostrando a execução dos comandos.](./media/create-vm/vm-certification-issues-solutions-25.png)

   >[!NOTE]
   >Este comando pode demorar algum tempo a ser concluído porque depende do tamanho do disco.

1. Retire o VHD da VM e apague o VM.


## <a name="default-credentials"></a>Credenciais padrão

Nunca envie credenciais padrão com o VHD submetido. A adição de credenciais padrão torna o VHD mais vulnerável a ameaças à segurança. Em vez disso, crie as suas próprias credenciais quando submeter o VHD.
  
## <a name="datadisk-mapped-incorrectly"></a>DataDisk mapeado incorretamente

Um problema de mapeamento pode ocorrer quando um pedido é submetido com vários discos de dados que não estão em sequência. Por exemplo, a ordem de numeração de três discos de dados deve ser *0, 1, 2*. Qualquer outra ordem é tratada como um problema de mapeamento.

Reenviar o pedido com a sequenciação adequada dos discos de dados.

## <a name="incorrect-os-mapping"></a>Mapeamento incorreto do SO

Quando uma imagem é criada, pode ser mapeada ou atribuída a etiqueta de SO errada. Por exemplo, quando seleciona o **Windows** como parte do nome DE enquanto está a criar a imagem, o disco OS deve ser instalado apenas com o Windows. O mesmo requisito aplica-se ao Linux.

## <a name="vm-not-generalized"></a>VM não generalizado

Se todas as imagens que são tiradas do Azure Marketplace forem reutilizadas, o sistema operativo VHD deve ser generalizado.

- Para **o Linux,** o processo seguinte generaliza um Linux VM e reimplanta-o como um VM separado.

  Na janela SSH, insira o seguinte comando: `sudo waagent -deprovision+user` .

- Para **o Windows,** generalize as imagens do Windows utilizando `sysreptool` .

  Para obter mais informações sobre a ferramenta, consulte a `sysreptool` [visão geral da preparação do sistema (Sysprep).](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)

## <a name="datadisk-errors"></a>Erros do DataDisk

Para soluções para erros relacionados com o disco de dados, utilize o seguinte quadro:

|Erro|Razão|Solução|
|---|---|---|
|`DataDisk- InvalidUrl:`|Este erro pode ocorrer devido a um número de unidade lógica inválido (LUN) quando a oferta é submetida.|Verifique se a sequência de números LUN para o disco de dados está no Partner Center.|
|`DataDisk- NotFound:`|Este erro pode ocorrer porque um disco de dados não está localizado num URL SAS especificado.|Verifique se o disco de dados está localizado no URL SAS especificado.|

## <a name="remote-access-issue"></a>Problema de acesso remoto

Obterá este erro se a opção "Remote Desktop Protocol" (RDP) não estiver ativada para a imagem do Windows.

Ativar o acesso rdp às imagens do Windows antes de as submeter.

## <a name="bash-history-failed"></a>A história da bash falhou

Verá este erro se o tamanho da história da Bash na sua imagem submetida for superior a 1 quilobyte (KB). O tamanho é restrito a 1 KB para restringir o ficheiro de conter informações potencialmente sensíveis.

Para apagar a história da Bash:

1. Implemente o VM e selecione a opção **Comando de Execução** no portal Azure.

   ![Screenshot do portal Azure com a opção 'Executar Comando' no painel esquerdo.](./media/create-vm/vm-certification-issues-solutions-3.png)

1. Selecione a primeira opção **RunShellScript** e, em seguida, executar o comando: `cat /dev/null > ~/.bash_history && history -c` .

   ![Screenshot da página 'Executar o Script do Comando' no portal Azure.](./media/create-vm/vm-certification-issues-solutions-4.png)

1. Depois de o comando correr com sucesso, reinicie o VM.

1. Generalize o VM, pegue a imagem VHD e pare o VM.

1. Reenviar a imagem generalizada.

## <a name="request-an-exception-on-vm-images-for-select-tests"></a>Solicite uma exceção nas imagens VM para testes selecionados

Os editores podem solicitar exceções para alguns testes realizados durante a certificação VM. As exceções são fornecidas em casos raros quando um editor fornece provas que corroboram o pedido. A equipa de Certificação reserva-se o direito de negar ou aprovar exceções a qualquer momento.

Esta secção descreve cenários gerais em que os editores solicitam uma exceção e como solicitar uma.

### <a name="scenarios-for-exception"></a>Cenários de exceção

Os editores geralmente pedem exceções nos seguintes casos:

- **Exceção para um ou mais casos de teste**. Contacte [o suporte do Partner Center](https://aka.ms/marketplacepublishersupport) para solicitar exceções para casos de teste.

- **VMs bloqueados / Sem acesso à raiz**. Algumas editoras têm cenários em que os VM precisam de ser bloqueados porque têm software como firewalls instalados no VM. Neste caso, faça o download da [Ferramenta de Teste Certificado](https://aka.ms/AzureCertificationTestTool) e submeta o relatório no suporte do Partner [Center](https://aka.ms/marketplacepublishersupport).

- **Modelos personalizados.** Algumas editoras publicam imagens VM que requerem um modelo personalizado do Azure Resource Manager (ARM) para implementar os VMs. Neste caso, envie os modelos personalizados no [suporte do Partner Center](https://aka.ms/marketplacepublishersupport) para que possa ser usado pela equipa de Certificação para validação.

### <a name="information-to-provide-for-exception-scenarios"></a>Informações para prever cenários de exceção

Contacte [o suporte do Partner Center](https://aka.ms/marketplacepublishersupport) para solicitar uma exceção para um dos cenários, e inclua as seguintes informações:

- **ID da editora.** Digite o seu portal Partner Central Editor ID.
- **Oferta ID/nome**. Insira o ID ou nome da Oferta.
- **Identificação SKU/Plano.** Digite o VM offer Plan ID ou SKU.
- **Versão**. Introduza a versão de oferta VM que requer uma exceção.
- **Tipo de Exceção**. Escolha entre testes, VM bloqueados ou modelos personalizados.
- **Razão de pedido.** Inclua o motivo do pedido de exceção, além de qualquer informação sobre isenções de teste.
- **Linha do tempo.** Insira a data final para a sua exceção.
- **Anexo**. Documentos de prova importantes anexados:

  - Para os VMs bloqueados, anexe o relatório de teste.
  - Para modelos personalizados, forneça o modelo ARM personalizado como acessório.

  Se não incluir estes anexos, o seu pedido será negado.

## <a name="address-a-vulnerability-or-an-exploit-in-a-vm-offer"></a>Endereçar uma vulnerabilidade ou uma exploração numa oferta VM

Esta secção descreve como fornecer uma nova imagem VM quando uma vulnerabilidade ou exploração é descoberta com uma das suas imagens VM. Aplica-se apenas às ofertas da Azure VM publicadas no Azure Marketplace.

> [!NOTE]
> Não pode remover a última imagem em VM de um plano ou parar de vender o último plano para uma oferta.

Faça uma das seguintes ações:

- Se tiver uma nova imagem em VM para substituir a imagem VM vulnerável, consulte [Fornecer uma imagem VM fixa](#provide-a-fixed-vm-image).
- Se não tiver uma nova imagem em VM para substituir a única imagem em VM num plano, ou se já terminou o plano, [pare de vender o plano.](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)
- Se não pretende substituir a única imagem VM da oferta, recomendamos que [deixe de vender a oferta.](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)

### <a name="provide-a-fixed-vm-image"></a>Fornecer uma imagem VM fixa

Para fornecer uma imagem VM fixa para substituir uma imagem VM que tenha uma vulnerabilidade ou exploração:

1. Forneça uma nova imagem em VM para abordar a vulnerabilidade de segurança ou exploração.
1. Remova a imagem VM com a vulnerabilidade de segurança ou exploração.
1. Republice a oferta.

#### <a name="provide-a-new-vm-image-to-address-the-security-vulnerability-or-exploit"></a>Fornecer uma nova imagem VM para abordar a vulnerabilidade de segurança ou explorar

Para completar estes passos, prepare os ativos técnicos para a imagem VM que pretende adicionar. Para obter mais informações, consulte [Criar uma máquina virtual utilizando uma base aprovada](azure-vm-create-using-approved-base.md) ou Criar uma máquina virtual utilizando a sua própria [imagem](azure-vm-create-using-own-image.md) e Gerar [um SAS URI para a sua imagem VM](azure-vm-get-sas-uri.md).

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
1. No painel esquerdo, selecione **Commercial Marketplace**  >  **Overview**.
1. Na coluna **'Oferta' é a** seguinte, selecione a oferta.
1. No separador **Plano de Visão Geral,** na coluna **Nome,** selecione o plano apropriado.
1. No separador **de configuração técnica,** em **Imagens VM,** selecione **+ Adicionar Imagem VM**.

   > [!NOTE]
   > Pode adicionar apenas uma imagem VM a um plano de cada vez. Para adicionar várias imagens VM, publique a primeira antes de adicionar a próxima imagem VM.

1. Nas caixas que aparecem, forneça uma nova versão em disco e a imagem da máquina virtual.
1. Selecione **Guardar rascunho**.

Em seguida, remova a imagem VM com a vulnerabilidade de segurança.

#### <a name="remove-the-vm-image-with-the-security-vulnerability-or-exploit"></a>Remova a imagem VM com a vulnerabilidade de segurança ou exploração

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No painel esquerdo, selecione **Commercial Marketplace**  >  **Overview**.
3. Na coluna **'Oferta' é a** seguinte, selecione a oferta.
4. No separador **Plano de Visão Geral,** na coluna **Nome,** selecione o plano apropriado.
5. No separador **de configuração técnica,** em **Imagens VM,** junto à imagem VM que pretende remover, selecione **Remover imagem VM**.
6. Na caixa de diálogo, **selecione Continue**.
7. Selecione **Guardar rascunho**.

Em seguida, reeditar a oferta.

#### <a name="republish-the-offer"></a>Reeminar a oferta

1. Selecione **Rever e publicar.**
2. Se precisar de fornecer alguma informação à equipa de certificação, adicione-a às Notas para caixa **de certificação.**
3. Selecione **Publicar**.

Para completar o processo de publicação, consulte [Rever e publicar ofertas.](review-publish-offer.md)

### <a name="vm-images-with-limited-access-or-requiring-custom-templates"></a>Imagens VM com acesso limitado ou requisitos personalizados

#### <a name="locked-down-or-ssh-disabled-offer"></a>Oferta de desativada SSH bloqueada (ou)

  As imagens publicadas com SSH desativadas (para Linux) ou RDP desativadas (para Windows) são tratadas como VMs bloqueados. Existem cenários de negócio especiais devido aos quais os Editores apenas permitem o acesso restrito a alguns utilizadores. Durante as verificações de validação, os VM bloqueados podem não permitir a execução de determinados comandos de certificação.


#### <a name="custom-templates"></a>Modelos personalizados

   Em geral, todas as imagens publicadas sob ofertas VM únicas seguirão o modelo padrão ARM para implementação. No entanto, existem cenários em que o editor pode necessitar de personalização durante a implementação de VMs (por exemplo, múltiplos NIC(s) a serem configurados).
    
   Dependendo dos cenários abaixo (não exaustivos), os editores utilizarão modelos personalizados para a implementação do VM:

   * O VM requer sub-redes de rede adicionais.
   * Metadados adicionais a serem inseridos no modelo ARM.
   * Comandos que são pré-requisitos para a execução do modelo ARM.

### <a name="vm-extensions"></a>Extensões de VM   

   As extensões de máquina virtual (VM) do Azure são pequenas aplicações que proporcionam tarefas de automação e configuração pós-implementação nas VMs do Azure. Por exemplo, se uma máquina virtual exigir a instalação de software, a proteção antivírus ou a execução de um script, poderá ser utilizada uma extensão de VM. 

   As validações de extensão Linux VM requerem que faça parte da imagem:
* Agente Azure Linux maior 2.2.41
* Versão python acima de 2.8 


Para mais informações, visite [a extensão VM.](../virtual-machines/extensions/diagnostics-linux.md)
     
## <a name="next-steps"></a>Passos seguintes

- [Configurar as propriedades da oferta de VM](azure-vm-create-properties.md)
- [Recompensas de mercado ativas](partner-center-portal/marketplace-rewards.md)
- Se tiver dúvidas ou feedback para melhorar, contacte [o suporte do Partner Center](https://aka.ms/marketplacepublishersupport).
