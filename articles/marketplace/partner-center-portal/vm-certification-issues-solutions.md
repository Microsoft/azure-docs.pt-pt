---
title: Questões comuns ao certificar imagens de máquinas virtuais para o Azure Marketplace
description: Este artigo explica mensagens de erro comuns e problemas ao testar e certificar imagens VM para o Azure Marketplace. Também discute soluções relacionadas.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 06/16/2020
ms.openlocfilehash: d724ef463d7c7ad237b5fd023e9c15f50de96f04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91803471"
---
# <a name="common-issues-when-certifying-virtual-machine-images-for-azure-marketplace"></a>Questões comuns ao certificar imagens de máquinas virtuais para o Azure Marketplace

Ao publicar a sua imagem de máquina virtual (VM) no Azure Marketplace, a equipa da Azure valida-a para garantir a sua compatibilidade de bootability, segurança e Azure. Se algum dos testes de alta qualidade falhar, a publicação falhará e receberá uma mensagem de erro que descreve o problema.

Este artigo explica mensagens de erro comuns durante a publicação de imagens VM, juntamente com soluções relacionadas.

> [!NOTE]
> Se tiver dúvidas ou feedback para melhorar, contacte [o Partner Center Support](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="approved-base-image"></a>Imagem base aprovada

Quando submete um pedido para republicar a sua imagem com atualizações, o caso de verificação de número parcial pode falhar. Se falhar, a sua imagem não será aprovada.

Esta falha ocorre quando se utiliza uma imagem base que pertence a outra editora e atualiza a imagem. Nesta situação, não poderá publicar a sua imagem.

Para corrigir este problema, recupere a imagem do Azure Marketplace e faça alterações no mesmo. Para obter mais informações, veja os artigos seguintes:

- [Imagens linux](../../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Imagens do Windows](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base)

> [!Note]
> Se estiver a utilizar uma imagem base linux não tirada do Marketplace, pode compensar a primeira partição até 2048 KB. Isto permite que o espaço não testado seja utilizado para adicionar novas informações de faturação e permite que a Azure avance com a publicação do seu VM no Marketplace.  

## <a name="vm-extension-failure"></a>Falha de extensão VM

Verifique se a sua imagem suporta extensões VM.

Para ativar as extensões de VM, faça o seguinte:

1. Selecione o seu Linux VM.
1. Ir para as **definições de Diagnóstico**.
1. Ativar as matrizes base atualizando a **conta de Armazenamento.**
1. Selecione **Guardar**.

   ![Ativar a monitorização ao nível do convidado](./media/vm-certification-issues-solutions-1.png)

Para verificar se as extensões VM estão corretamente ativadas, faça o seguinte:

1. No VM, selecione o separador **de extensões VM** e, em seguida, verifique o estado da extensão de **diagnóstico linux**.
    * Se o estado for *Provisioning Succeeded,* o caso do teste de extensões passou.  
    * Se o estado for *Provisioning Failed,* o caso de teste de extensões falhou e tem de definir a bandeira endurecida.

      ![Screenshot mostrando que o provisionamento foi bem sucedido](./media/vm-certification-issues-solutions-2.png)

      Se a extensão VM falhar, consulte [a extensão de diagnóstico do Linux para monitorizar métricas e registos](../../virtual-machines/extensions/diagnostics-linux.md) para a ativar. Se não quiser ativar a extensão VM, contacte a equipa de Suporte e peça-lhes que a desativem.

## <a name="vm-provisioning-issue"></a>Emissão de provisionamento de VM

Verifique se seguiu rigorosamente o processo de provisionamento de VM antes de submeter a sua oferta. Para visualizar o formato JSON para o fornecimento do VM, consulte a [certificação de imagem da máquina virtual Azure](azure-vm-image-certification.md).

As questões de provisionamento podem incluir os seguintes cenários de falha:

|Cenário|Erro|Razão|Solução|
|---|---|---|---|
|1|Disco rígido virtual inválido (VHD)|Se o valor de cookie especificado no rodapé VHD estiver incorreto, o VHD será considerado inválido.|Re-crie a imagem e envie o pedido.|
|2|Tipo de bolha inválida|O fornecimento de VM falhou porque o bloco usado é um tipo de bolha em vez de um tipo de página.|Re-crie a imagem e envie o pedido.|
|3|Provisionamento do tempo limite ou não devidamente generalizado|Há um problema com a generalização da VM.|Re-crie a imagem com generalização e submeta o pedido.|

> [!NOTE]
> Para obter mais informações sobre a generalização da VM, consulte:
> - [Documentação linux](create-azure-vm-technical-asset.md#generalize-the-image)
> - [Documentação do Windows](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)

## <a name="software-compliance-for-windows"></a>Conformidade de software para Windows

Se o seu pedido de imagem do Windows for rejeitado devido a um problema de conformidade com o software, poderá ter criado uma imagem do Windows com a instância do servidor SQL instalada em vez de tirar a imagem base da versão SQL relevante do Azure Marketplace.

Não crie a sua própria imagem Windows com servidor SQL instalado na mesma. Em vez disso, utilize as imagens de base SQL aprovadas (Enterprise/Standard/web) do Azure Marketplace.

Se estiver a tentar instalar o Visual Studio ou qualquer produto licenciado pelo Office, contacte a equipa de Suporte para obter aprovação prévia.

Para obter mais informações sobre a seleção de uma base aprovada, consulte [Criar os seus ativos técnicos da máquina virtual Azure](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base).

## <a name="tool-kit-test-case-execution-failed"></a>A execução do caso de teste do kit de ferramenta falhou 

O conjunto de ferramentas de certificação da Microsoft pode ajudá-lo a executar casos de teste e verificar se o seu VHD ou imagem é compatível com o ambiente Azure.

Descarregue o [kit de ferramentas da Microsoft Certification](azure-vm-image-certification.md).

## <a name="linux-test-cases"></a>Casos de teste linux

A tabela que se segue lista os casos de teste Linux que o conjunto de ferramentas irá executar. A validação do teste está indicada na descrição.

|Cenário|Caso de teste|Descrição|
|---|---|---|
|1|História da bash|Os ficheiros de histórico de bash devem ser limpos antes de criar a imagem VM.|
|2|Versão linux agent|O Agente Azure Linux 2.2.41 ou mais tarde deve ser instalado.|
|3|Parâmetros de núcleo necessários|Verifica se os seguintes parâmetros do núcleo são definidos: <br>console=ttyS0<br>earlyprintk=ttyS0<br>rootdelay=300|
|4|Trocar divisórias no disco DE|Verifica que as divisórias de troca não são criadas no disco de so.|
|5|Partição de raiz no disco de SO|Crie uma única divisória de raiz para o disco DE.|
|6|Versão OpenSSL|A versão OpenSSL deve ser v0.9.8 ou posterior.|
|7|Versão de Python|A versão python 2.6 ou mais tarde é altamente recomendada.|
|8|Intervalo de cliente vivo|Desafie o ClientAliveInterval a 180. Sobre a necessidade de aplicação, pode ser definido de 30 a 235. Se estiver a ativar o SSH para os seus utilizadores finais, este valor deve ser definido como explicado.|
|9|Arquitetura de SO|Apenas os sistemas operativos de 64 bits são suportados.|
|10|Atualização automática|Identifica se a atualização automática do agente Linux está ativada.|

### <a name="common-errors-found-while-executing-previous-test-cases"></a>Erros comuns encontrados durante a execução de casos de teste anteriores

A tabela que se segue enumera erros comuns que são encontrados durante a execução de casos de teste anteriores:
 
|Cenário|Caso de teste|Erro|Solução|
|---|---|---|---|
|1|Caso de teste de versão linux agente|A versão mínima do agente Linux é 2.2.41 ou mais tarde. Esta exigência é obrigatória desde 1 de maio de 2020.|Por favor, atualize a versão do agente Linux e deverá ser 2.241 ou mais tarde. Para mais informações, pode visitar a [página de atualização da versão do Agente Linux](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).|
|2|Caso de teste de história de bash|Verá um erro se o tamanho do histórico de bash na sua imagem submetida for superior a 1 quilobyte (KB). O tamanho é restrito a 1 KB para garantir que qualquer informação potencialmente sensível não seja capturada no seu ficheiro histórico de bash.|Para resolver este problema, monte o VHD em qualquer outro VM de trabalho e faça quaisquer alterações que pretenda (por exemplo, eliminar os ficheiros *históricos .bash)* para reduzir o tamanho para menos ou igual a 1 KB.|
|3|Caso de teste do parâmetro do núcleo necessário|Receberá este erro quando o valor para **a consola** não estiver definido para **ttyS0**. Verifique executando o seguinte comando:<br>`cat /proc/cmdline`|Descreva o valor da **consola** para **o ttyS0**e reenvia o pedido.|
|4|Caso de teste de intervalo clientealive|Se o resultado do toolkit lhe der um resultado falhado para este caso de teste, existe um valor inadequado para **o ClientAliveInterval**.|Descreva o valor para **ClientAliveInterval** para menos ou igual a 235 e, em seguida, reenvia o pedido.|

### <a name="windows-test-cases"></a>Casos de teste do Windows

A tabela que se segue lista os casos de teste do Windows que o conjunto de ferramentas irá executar, juntamente com uma descrição da validação do teste:

|Cenário |Casos de teste|Descrição|
|---|---|---|---|
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

Se encontrar falhas com os casos de teste anteriores, consulte a coluna **Descrição** na tabela para obter a solução. Se precisar de mais informações, contacte a equipa de Apoio. 

## <a name="data-disk-size-verification"></a>Verificação do tamanho do disco de dados

Se o tamanho de qualquer pedido que seja submetido com o disco de dados for superior a 1023 gigabytes (GB), o pedido não será aprovado. Esta regra aplica-se tanto ao Linux como ao Windows.

Reenviar o pedido com um tamanho inferior ou igual a 1023 GB.

## <a name="os-disk-size-validation"></a>Validação do tamanho do disco de OS

Consulte as seguintes regras para limitações no tamanho do disco de SO. Quando submeter qualquer pedido, verifique se o tamanho do disco OS está dentro da limitação para Linux ou Windows.

|SO|Tamanho VHD recomendado|
|---|---|
|Linux|30 GB a 1023 GB|
|Windows|30 GB a 250 GB|

Como os VMs permitem o acesso ao sistema operativo subjacente, certifique-se de que o tamanho VHD é suficientemente grande para o VHD. Como os discos não são expansíveis sem tempo de inatividade, use um tamanho de disco de 30 GB a 50 GB.

|Tamanho VHD|Tamanho real ocupado|Solução|
|---|---|---|
|>500 tebibytes (TiB)|n/a|Contacte a equipa de Apoio para obter uma aprovação de exceção.|
|250-500 TiB|>diferença de 200 gibibytes (GiB) do tamanho do blob|Contacte a equipa de Apoio para obter uma aprovação de exceção.|

> [!NOTE]
> Os tamanhos de disco maiores incorrem em custos mais elevados e incorrerão num atraso durante o processo de configuração e replicação. Devido a este atraso e custo, a equipa de Apoio poderá procurar justificação para a aprovação da exceção.

## <a name="wannacry-patch-verification-test-for-windows"></a>Teste de verificação de patch do WannaCry para Windows

Para evitar um possível ataque relacionado com o vírus WannaCry, certifique-se de que todos os pedidos de imagem do Windows são atualizados com o mais recente patch.

Para verificar a versão corrigida do Windows Server para o detalhe oss e a versão mínima que irá suportar, consulte a tabela seguinte: 

A versão do ficheiro de imagem pode ser verificada a partir `C:\windows\system32\drivers\srv.sys` de ou `srv2.sys` .

> [!NOTE]
> O Windows Server 2019 não tem quaisquer requisitos de versão obrigatória.

|SO|Versão|
|---|---|
|Windows Serve 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|ND|

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
||14.04 LTS|4.15.0-1049-*-azure|
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
|CoreOS Estável 2079.6.0|4.19.43*|
||Beta 2135.3.1|4.19.50*|
||Alfa 2163.2.1|4.19.50*|
|Debian|jessie (segurança)|3.16.68-2|
||jessie backports|4.9.168-1+deb9u3|
||alongamento (segurança)|4.9.168-1+deb9u3|
||Debian GNU/Linux 10 (buster)|Debian 6.3.0-18+deb9u1|
||buster, sid (extensões backports)|4.19.37-5|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>O tamanho da imagem deve estar em múltiplos de megabytes

Todos os VHDs em Azure devem ter um tamanho virtual alinhado a múltiplos de 1 megabyte (MB). Se o seu VHD não aderir ao tamanho virtual recomendado, o seu pedido poderá ser rejeitado.

Siga as diretrizes quando estiver a converter de um disco cru para VHD e certifique-se de que o tamanho do disco bruto é um múltiplo de 1 MB. Para obter mais informações, consulte [informações para distribuição não endossada.](../../virtual-machines/linux/create-upload-generic.md)

## <a name="vm-access-denied"></a>Acesso VM negado

Se encontrar acesso a problemas negados enquanto está a executar os casos de teste no VM, pode ser devido a privilégios insuficientes para executar os casos de teste.

Verifique se o acesso adequado está ativado para a conta em que os casos de auto-teste estão a decorrer. Se o acesso não estiver ativado, ative-o para executar os casos de teste. Se não quiser ativar o acesso, poderá partilhar os resultados do caso de auto-teste com a equipa de Suporte.

## <a name="download-failure"></a>Falha no download
    
Consulte a tabela seguinte para quaisquer problemas que surjam quando descarrega a imagem VM utilizando um URL de assinatura de acesso partilhado (SAS).

|Cenário|Erro|Razão|Solução|
|---|---|---|---|
|1|Blob não encontrado|O VHD pode ser eliminado ou deslocado do local especificado.|| 
|2|Bolha em uso|O VHD é utilizado por outro processo interno.|O VHD deve estar num estado usado quando o descarrega utilizando um URL SAS.|
|3|URL DE SAS inválido|O URL SAS associado para o VHD está incorreto.|Obtenha o URL SAS correto.|
|4|Assinatura inválida|O URL SAS associado para o VHD está incorreto.|Obtenha o URL SAS correto.|
|6|Cabeçalho condicional HTTP|O URL SAS é inválido.|Obtenha o URL SAS correto.|
|7|Nome VHD inválido|Verifique se algum personagem especial, como um sinal por cento (%) ou aspas ("), existem no nome VHD.|Mude o nome do ficheiro VHD removendo os caracteres especiais.|

## <a name="first-mb-2048-kb-partition-only-for-linux"></a>Primeira partição MB (2048 KB) (Apenas para Linux)

Quando submeter o VHD, certifique-se de que o primeiro KB de 2048 do VHD está vazio. Caso contrário, o seu pedido falhará*.

>[!NOTE]
>*Para certas imagens especiais, como as construídas em cima das imagens base do Azure Windows tiradas do Azure Marketplace, verificamos uma etiqueta de Faturação e ignoramos a partição MB se a etiqueta de faturação estiver presente e corresponder aos nossos valores internos disponíveis.

## <a name="default-credentials"></a>Credenciais padrão

Certifique-se sempre de que as credenciais predefinidas não são enviadas com o VHD submetido. A adição de credenciais padrão torna o VHD mais vulnerável a ameaças à segurança. Em vez disso, crie as suas próprias credenciais quando submeter o VHD.
  
## <a name="datadisk-mapped-incorrectly"></a>DataDisk mapeado incorretamente

Quando um pedido é submetido com vários discos de dados, mas a sua encomenda não está em sequência, este é considerado um problema de mapeamento. Por exemplo, se existirem três discos de dados, a ordem de numeração deve ser *0, 1, 2*. Qualquer outra ordem é tratada como um problema de mapeamento.

Reenviar o pedido com a sequenciação adequada dos discos de dados.

## <a name="incorrect-os-mapping"></a>Mapeamento incorreto do SO

Quando uma imagem é criada, pode ser mapeada ou atribuída a etiqueta de SO errada. Por exemplo, quando seleciona o **Windows** como parte do nome DE enquanto está a criar a imagem, o disco OS deve ser instalado apenas com o Windows. O mesmo requisito aplica-se ao Linux.

## <a name="vm-not-generalized"></a>VM não generalizado

Se todas as imagens que são tiradas do Azure Marketplace forem reutilizadas, o sistema operativo VHD deve ser generalizado.

* Para **o Linux,** o processo seguinte generaliza um Linux VM e reimplanta-o como um VM separado.

  Na janela SSH, insira o seguinte comando: `sudo waagent -deprovision+user`

* Para **o Windows,** generalize as imagens do Windows utilizando `sysreptool` .

Para obter mais informações sobre esta ferramenta, consulte [a visão geral da preparação do sistema (Sysprep).]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)

## <a name="datadisk-errors"></a>Erros do DataDisk

Para soluções para erros relacionados com o disco de dados, utilize o seguinte quadro:

|Erro|Razão|Solução|
|---|---|---|
|`DataDisk- InvalidUrl:`|Este erro pode ocorrer devido a um número inválido especificado para o número de unidade lógica (LUN) quando a oferta é submetida.|Verifique se a sequência de números LUN para o disco de dados está no Partner Center.|
|`DataDisk- NotFound:`|Este erro pode ocorrer devido a um disco de dados não estar localizado num URL SAS especificado.|Verifique se o disco de dados está localizado no URL SAS especificado no pedido.|

## <a name="remote-access-issue"></a>Problema de acesso remoto

Se a opção "Remote Desktop Protocol" (RDP) não estiver ativada para a imagem do Windows, receberá este erro. 

Ativar o acesso rdp às imagens do Windows antes de as submeter.

## <a name="bash-history-failed"></a>A história da bash falhou

Verá este erro se o tamanho do histórico de bash na sua imagem submetida for superior a 1 quilobyte (KB). O tamanho é restrito a 1 KB para garantir que qualquer informação potencialmente sensível não seja capturada no seu ficheiro histórico de bash.

Abaixo estão os passos para apagar a "História da Bash".

Passo 1. Implemente o VM e clique na opção "Executar comando" no portal Azure.
![Executar comando no portal Azure](./media/vm-certification-issues-solutions-3.png)

Passo 2. Selecione a primeira opção "RunShellScript" e execute o comando abaixo.

Comando: "cat /dev/null > ~/.bash_history && history -c" ![ Bash History command on Azure portal](./media/vm-certification-issues-solutions-4.png)

Passo 3. Após a execução bem sucedida do comando, reinicie o VM.

Passo 4: Generalize o VM, pegue o VHD de imagem e pare o VM.

Passo 5.     Re-Submit a imagem generalizada.

## <a name="requesting-exceptions-custom-templates-on-vm-images-for-selective-tests"></a>Solicitando exceções (modelos personalizados) em imagens VM para testes seletivos

Os editores podem solicitar exceções para poucos testes realizados durante a certificação VM. As exceções são fornecidas em casos extremamente raros quando o editor fornece provas que corroboram o pedido.
A equipa de certificação reserva-se o direito de negar ou aprovar exceções em qualquer momento.

Nas secções abaixo, falaremos sobre os principais cenários em que são pedidas exceções e como solicitar exceção.

Cenários de exceção

Existem três cenários/casos em que os editores geralmente solicitam estas exceções. 

* **Exceção para um ou mais casos de teste:** Os editores podem contactar o [Marketplace Publisher Support pedir](https://aka.ms/marketplacepublishersupport) exceções para casos de teste. 

* **VMs bloqueados/Sem acesso à raiz:** Poucas editoras têm cenários em que os VM precisam de ser bloqueados, uma vez que têm software como firewalls instalados no VM. 
       Neste caso, os editores podem descarregar a [Ferramenta de Teste Certificada](https://aka.ms/AzureCertificationTestTool) aqui, e fornecer o relatório no [Marketplace Publisher Support](https://aka.ms/marketplacepublishersupport)


* **Modelos personalizados:** Alguns editores publicam imagens VM que requerem um modelo ARM personalizado para implementar os VMs. Neste caso, solicita-se aos Editores que forneçam os modelos personalizados no [Marketplace Publisher Support](https://aka.ms/marketplacepublishersupport) para que o mesmo possa ser utilizado pela equipa de Certificação para validação. 

### <a name="information-to-provide-for-exception-scenarios"></a>Informações para prever cenários de exceção

Os editores devem contactar o suporte no [Marketplace Publisher Support](https://aka.ms/marketplacepublishersupport) para solicitar exceções para o cenário acima referido com as seguintes informações adicionais:

   1.   Editor ID – O ID da editora no portal Partner Center
   2.   ID/nome da oferta – O ID/nome da oferta para o qual é solicitada a exceção 
   3.   SKU/Plan ID – O plano ID/sku da oferta VM para a qual é solicitada exceção
   4.    Versão – A versão da oferta VM para a qual é pedida exceção
   5.   Tipo de exceção (Testes, VM bloqueados, modelos personalizados
   6.   Razão de pedido – Razão para esta exceção e informação sobre testes a isentar 
   7. Linha do tempo - Data até à qual esta exceção foi solicitada 
   8.   Anexo - Anexar quaisquer documentos de prova de importância. Para VMs bloqueados, anexe o relatório de teste e os modelos personalizados, forneça o modelo ARM personalizado como acessório. A não anexação do relatório para VMs bloqueados e o modelo ARM personalizado para modelos personalizados resultará em negação de pedido


## <a name="next-steps"></a>Passos seguintes

Se tiver dúvidas ou feedback para melhorar, contacte [o Partner Center Support](https://partner.microsoft.com/support/v2/?stage=1).
