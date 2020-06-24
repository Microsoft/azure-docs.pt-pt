---
title: Certificação de máquinas virtuais - problemas e soluções
description: Este artigo explica mensagens de erro comuns para imagens VM. Também discute soluções relacionadas
author: v-miegge
ms.author: v-krmall
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
ms.date: 06/16/2020
ms.openlocfilehash: dcf687a369b32b2055f579f2599a0c3097f9f9f3
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84977765"
---
# <a name="virtual-machine-certification---issues-and-solutions"></a>Certificação de máquinas virtuais - problemas e soluções

Ao publicar as suas imagens de máquina virtual (VM) no Azure Marketplace, a equipa do Azure valida a imagem VM para garantir a sua capacidade de arranque, segurança e compatibilidade Azure. Se algum dos testes de alta qualidade falhar, a publicação falhará com uma mensagem que contenha o erro.

Este artigo explica mensagens de erro comuns para imagens VM. Também discute soluções relacionadas:

> [!NOTE]
> Se tiver dúvidas ou feedback para melhorar, contacte [o Partner Center Support](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="approved-base-image"></a>Imagem base aprovada

Quando submete um pedido para republicar a sua imagem com atualizações, o caso de verificação de número parcial pode falhar. Nesse caso, a sua imagem não será aprovada.

Esta falha ocorrerá quando usou uma imagem base que pertence a outra editora e atualizou a imagem. Nesta situação, não poderá publicar a sua imagem.

Para corrigir este problema, recupere a sua última imagem do Azure Marketplace e faça alterações nessa imagem. Veja o seguinte para ver as imagens base aprovadas onde pode pesquisar a sua imagem:

- [Imagens Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros?toc=/azure/virtual-machines/linux/toc.json)
- [Imagens windows](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd#select-an-approved-base)

## <a name="vm-extension-failure"></a>Falha de extensão VM

Para verificar se a sua imagem suporta ou não a Extensão VM, siga estes passos:

Ativar extensões VM:

1. Selecione o Linux VM.
2. Ir para as **definições de Diagnóstico**.
3. Ativar as matrizes base atualizando a **conta de Armazenamento.**
4. Selecione **Guardar**.

   ![Ativar a monitorização ao nível do convidado](./media/vm-certification-issues-solutions-1.png)

Verifique se as extensões VM estão corretamente ativadas:

5. Vá ao separador de **extensões VM** do VM e verifique a extensão de **diagnóstico linux**.
6. Se o Estado está **a prever bem sucedido,** o caso do teste de extensões passou.
7. Se o Estado de **Provisionamento falhar,** então o caso do teste de extensões falhou e tem de definir a bandeira endurecida.

   ![Provisão conseguiu](./media/vm-certification-issues-solutions-2.png)

   Se a extensão VM falhar, vá utilizar a [extensão de diagnóstico do Linux para monitorizar métricas e registos](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux) para a ativar. Se não quiser ativar a extensão VM, contacte a equipa de apoio e peça-lhes que desativem a extensão.

## <a name="virtual-machine-provisioning-issue"></a>Problema de fornecimento de máquinas virtuais

Verifique se o processo de provisionamento é rigorosamente seguido para o VM antes de submeter a sua oferta. Para ver o formato json para o provisionamento do VM, aceda à [certificação de imagem da Azure Virtual Machine (VM).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template)

As questões de provisionamento podem incluir os seguintes cenários de falha:

|S.NO|erro|reason|solução|
|---|---|---|---|
|1|Disco rígido virtual inválido (VHD)|Se o valor especificado de cookies no rodapé VHD não estiver correto, então o VHD será considerado inválido.|Recrie a imagem e submeta o pedido.|
|2|Tipo de bolha inválida|O fornecimento de VM falhou porque o bloco usado é um tipo de bolha em vez de um tipo de página.|Recrie a imagem e submeta o pedido.|
|3|Provisionamento do tempo limite ou não devidamente generalizado|Há um problema com a generalização da VM.|Recrie a imagem com generalização e submeta o pedido.|

> [!NOTE]
> Siga estes links para documentação relacionada com a generalização da VM:
> - [Linux](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-configure-vm#generalize-the-image)
> - [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)

## <a name="software-compliance-for-windows"></a>Conformidade de software para Windows

Se o seu pedido de imagem do Windows for rejeitado devido à conformidade com o software, então pode ter criado uma imagem do Windows com o servidor SQL instalado, em vez de tirar a imagem base da versão SQL relevante do Azure Marketplace.

Não crie a sua própria imagem de janela com servidor SQL instalado nele. Em vez disso, utilize as imagens de base SQL aprovadas (Enterprise/Standard/web) do Azure Marketplace.

Se estiver a tentar instalar o estúdio Visual ou qualquer produto licenciado em escritório, contacte a equipa de apoio para obter aprovação prévia.

Para mais informações, visite [Crie os seus ativos técnicos da Azure Virtual Machine](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd#select-an-approved-base) para selecionar uma base aprovada.

## <a name="tool-kit-test-case-execution-failed"></a>A execução do caso de teste do kit de ferramenta falhou

O conjunto de ferramentas de certificação da Microsoft irá ajudá-lo a executar casos de teste verificar se o seu VHD/Image é compatível com o ambiente Azure.

Descarregue o [kit de ferramentas da Microsoft Certification](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

## <a name="linux-test-cases"></a>Casos de teste linux

Seguem-se os casos de teste Linux que o conjunto de ferramentas executará. A validação do teste está indicada na descrição.

|S.No|casos de teste|descrição|
|---|---|---|
|1|História de Bash|Os ficheiros de histórico de bash devem ser limpos antes de criar a imagem VM.|
|2|Versão do agente Linux|Azure Linux Agent 2.2.41 e além deve ser instalado.|
|3|Parâmetros kernel necessários|Verifica se estão definidos os seguintes parâmetros do núcleo: <br>console=ttyS0<br>earlyprintk=ttyS0<br>rootdelay=300|
|4|Trocar partição no disco OS|Verifica que as divisórias de troca não são criadas no disco de so.|
|5|Partição de raiz no disco oss|Crie uma única divisória de raiz para o disco SO.|
|6|Versão OpenSSL|A versão OpenSSL deve ser maior ou igual a v0.9.8.|
|7|Versão Python|A versão python 2.6+ é altamente recomendada.|
|8|Intervalo de cliente vivo|Desafie o ClientAliveInterval a 180. Sobre a necessidade de candidatura, pode ser definido entre 30 a 235. Se estiver a ativar o SSH para os seus utilizadores finais, este valor deve ser definido como explicado.|
|9|Arquitetura OS|Apenas os sistemas operativos de 64 bits são suportados.|
|10|Atualização automática|Identifica se a atualização automática do agente Linux está ativada.|

### <a name="common-errors-found-while-executing-the-previous-test-cases"></a>Erros comuns encontrados durante a execução dos casos de teste anteriores

Erros comuns encontrados durante a execução dos casos de teste anteriores.
 
|S.NO|caso de teste|erro|solução|
|---|---|---|---|
|1|Caso de teste da versão do agente Linux|A versão mínima do agente Linux é 2.241 ou superior. Esta exigência é obrigatória desde 1 de maio de 2020|A imagem deve ser atualizada com a versão requerida para [apresentar o pedido](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).|
|2|Caso de teste de história de Bash|Verá um erro se o tamanho do histórico de bash na sua imagem submetida for superior a 1 KB. O tamanho é restrito a 1 KB para garantir que qualquer informação potencialmente sensível não seja capturada no seu ficheiro histórico de bash.|Para resolver este problema, monte o VHD em qualquer outro VM de trabalho e faça quaisquer alterações (por exemplo, elimine os `.bash` ficheiros de histórico) deseja reduzir o tamanho para menos ou igual a 1 KB.|
|3|Caso de teste do parâmetro kernel necessário|Receberá este erro quando o valor para **a consola** não estiver definido para **ttyS0**. Verifique executando o comando:<br>`cat /proc/cmdline`|Descreva o valor da **consola** para **o ttyS0** e reenvia o pedido.|
|4|Caso de teste de intervalo de clientealive|Se o resultado do toolkit lhe der um resultado falhado para este caso de teste, existe um valor inadequado para **o ClientAliveInterval**.|Descreva o valor para **ClientAliveInterval** para menos ou igual a 235, em seguida, reenviar o pedido.|

### <a name="windows-test-cases"></a>Casos de teste do Windows

Seguem-se os casos de teste do Windows que o conjunto de ferramentas irá executar. A validação do teste está indicada na descrição.

|S.No|casos de teste|descrição|
|---|---|---|---|
|1|Arquitetura OS|O Azure suporta apenas sistemas operativos de 64 bits.|
|2|Dependência da conta do utilizador|A execução da aplicação não deve depender da conta do administrador.|
|3|Cluster de Ativação Pós-falha|A funcionalidade de Clustering failover do Windows Server ainda não está suportada. a aplicação não deve depender desta funcionalidade.|
|4|IPV6|O IPv6 ainda não é apoiado no ambiente Azure. A aplicação não deve depender desta funcionalidade.|
|5|DHCP|O papel do Servidor do Protocolo de Configuração do Anfitrião Dinâmico ainda não está suportado. A aplicação não deve depender desta funcionalidade.|
|6|Hyper-V|O papel do Servidor Hiper-V ainda não está suportado. A aplicação não deve depender desta funcionalidade.|
|7|Acesso Remoto|A função do servidor acesso remoto (Acesso Direto) ainda não está suportada. A aplicação não deve depender desta funcionalidade.|
|8|Serviços de Gestão de Direitos|Serviços de Gestão de Direitos. O papel do servidor ainda não está suportado. A aplicação não deve depender desta funcionalidade.|
|9|Windows Deployment Services|Serviços de implantação do Windows. O papel do servidor ainda não está suportado. A aplicação não deve depender desta funcionalidade.|
|10|Encriptação de Unidade BitLocker|A encriptação BitLocker Drive não é suportada no disco rígido do sistema operativo, mas pode ser usada em discos de dados.|
|11|Servidor de nome de armazenamento de internet|A funcionalidade do Servidor de Nome de Armazenamento de Internet ainda não está suportada. A aplicação não deve depender desta funcionalidade.|
|12|Multipath I/O|I/O multipata. Esta funcionalidade do servidor ainda não está suportada. A aplicação não deve depender desta funcionalidade.|
|13|Balanceamento de Carga na Rede|Equilíbrio de carga de rede. Esta funcionalidade do servidor ainda não está suportada. A aplicação não deve depender desta funcionalidade.|
|14|Protocolo PNRP (Peer Name Resolution Protocol)|Protocolo de Resolução de Nome de Pares. Esta funcionalidade do servidor ainda não está suportada. A aplicação não deve depender desta funcionalidade.|
|15|Serviços SNMP|A funcionalidade serviços SNMP ainda não está suportada. A aplicação não deve depender desta funcionalidade.|
|16|Serviço de Nome da Internet do Windows|Serviço de Nome da Internet do Windows. Esta funcionalidade do servidor ainda não está suportada. A aplicação não deve depender desta funcionalidade.|
|17|Serviço LAN Sem Fios|Serviço LAN sem fios. Esta funcionalidade do servidor ainda não está suportada. A aplicação não deve depender desta funcionalidade.|

Se encontrar falhas com os casos de teste acima, consulte a coluna **Descrição** na tabela anterior para obter a solução. Se precisar de mais informações, contacte a equipa de apoio. 

## <a name="data-disk-size-verification"></a>Verificação do tamanho do disco de dados

Se o tamanho de qualquer pedido submetido com o disco de dados for superior a 1023 GB, esse pedido não será aprovado. Esta regra aplica-se tanto ao Linux & Windows.

reenviar o pedido com um tamanho inferior ou igual a 1023 GB.

## <a name="os-disk-size-validation"></a>Validação do tamanho do disco de OS

Consulte as seguintes regras para limitações no tamanho do disco de SO. Quando submeter qualquer pedido, verifique se o tamanho do disco OS está dentro da limitação para Linux ou Windows.

|SO|tamanho VHD recomendado|
|---|---|
|Linux|30 GB a 1023 GB|
|Windows|30 GB a 250 GB|

Como os VMs permitem o acesso ao sistema operativo subjacente, certifique-se de que o tamanho VHD é suficientemente grande para o VHD. Como os discos não são expansíveis sem tempo de inatividade, use um tamanho de disco entre 30 e 50 GB.

|Tamanho VHD|tamanho real ocupado|solução|
|---|---|---|
|>500 TiB|n/a|contacte a equipa de apoio para obter uma aprovação de exceção.|
|250-500 TiB|>200 GiB diferente do tamanho da bolha|contacte a equipa de apoio para obter uma aprovação de exceção.|

> [!NOTE]
> Os tamanhos de disco maiores incorrem em custos mais elevados e incorrerão num atraso durante as etapas de provisionamento e replicação. Devido a este atraso e custo, a equipa de apoio pode pedir justificação para a aprovação da exceção.

## <a name="wannacry-patch-verification-test-for-windows"></a>Teste de verificação de patch do WannaCry para Windows

Para evitar um possível ataque relacionado com o vírus WannaCry, certifique-se de que todos os pedidos de imagem do Windows são atualizados com o mais recente patch.

Para verificar a versão corrigida do Windows Server, consulte a tabela seguinte para o detalhe de OS e a versão mínima que irá suportar. 

A versão do ficheiro de imagem pode ser verificada a partir `C:\windows\system32\drivers\srv.sys` de ou `srv2.sys` .

> [!NOTE]
> O WindowsServer2019 não tem requisitos de versão obrigatória.

|SO|versão|
|---|---|
|WindowsServer2008R2|6.1.7601.23689|
|WindowsServer2012|6.2.9200.22099|
|WindowsServer2012R2|6.3.9600.18604|
|WindowsServer2016|10.0.14393.953|
|WindowsServer2019|ND|

## <a name="sack-vulnerability-patch-verification"></a>Verificação de patch de vulnerabilidade SACK

Quando submete uma imagem Linux, o seu pedido pode ser rejeitado devido a problemas de versão kernel.

Atualize o núcleo com uma versão aprovada e reenvia o pedido. Pode encontrar a versão de kernel aprovada na tabela seguinte. O número da versão deve ser igual ou superior ao indicado abaixo.

Se a sua imagem não estiver instalada com uma das seguintes versões de kernel, atualize a sua imagem com as correções corretas. Pode encontrar mais informações a partir dos seguintes links. Solicite a aprovação necessária da equipa de apoio após a atualização da imagem com estes patches necessários:

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|Família osa|versão|kernel|
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

Todos os VHDs em Azure devem ter um tamanho virtual alinhado a múltiplos de 1 MB. Se o seu VHD não aderir ao tamanho virtual recomendado, o seu pedido poderá ser rejeitado.

Siga as diretrizes, quando estiver a converter de um disco cru para VHD e deve certificar-se de que o tamanho do disco bruto é um múltiplo de 1 MB. Para obter mais informações, consulte [informações sobre distribuições não endossadas](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic)

## <a name="vm-access-denied"></a>Acesso VM negado

Se encontrar acesso a problemas negados durante a execução dos casos de teste em VM, pode ser devido a privilégios insuficientes para executar os casos de teste.

Verifique se o acesso adequado está ativado para a conta em que os casos de auto-teste estão a ser executados. se não estiverem, permitir o acesso à execução dos casos de teste. Se não quiser ativar o acesso, poderá partilhar os resultados do caso de auto-teste com a equipa de suporte.

## <a name="download-failure"></a>Falha no download
    
Consulte a tabela seguinte para quaisquer problemas ao descarregar a imagem VM utilizando o URL SAS.

|S.NO|erro|reason|solução|
|---|---|---|---|
|1|Blob não encontrado|O VHD pode ser eliminado ou movido do local especificado|| 
|2|Bolha em uso|O VHD é usado por outro processo interno|O VHD deve estar num estado usado ao descarregar usando o URL SAS.|
|3|URL DE SAS inválido|O URL SAS associado para o VHD está incorreto.|Obtenha o URL SAS correto.|
|4|Assinatura Inválida|O URL SAS associado para o VHD está incorreto.|Obtenha o URL SAS correto.|
|6|Cabeçalho condicional HTTP|URL SAS INVÁLIDO.|Obtenha o URL SAS correto.|
|7|Nome VHD inválido|Verifique se existem caracteres especiais como **%** ou **" "** no nome VHD|Mude o nome do ficheiro VHD removendo caracteres especiais|

## <a name="first-1-mb-partition"></a>Primeira partição de MB de 1 MB

Ao submeter o VHD, certifique-se de que a primeira divisão de 1 MB do VHD está vazia. Caso contrário, o seu pedido falhará.

## <a name="default-credentials"></a>Credenciais padrão

Certifique-se sempre de que as credenciais predefinidas não são enviadas com o VHD submetido. A adição de credenciais padrão torna o VHD mais vulnerável a ameaças à segurança. Em vez disso, crie as suas próprias credenciais ao submeter o VHD.
  
## <a name="datadisk-mapped-incorrectly"></a>DataDisk mapeado incorretamente

Quando um pedido é submetido com vários discos de dados, mas a sua encomenda não está em sequência, este é considerado um problema de mapeamento. Por exemplo, se existirem três discos de dados, a ordem de numeração deve ser **0, 1, 2**. Qualquer outra ordem será tratada como um problema de mapeamento.

reenviar o pedido com a sequenciação adequada dos discos de dados.

## <a name="incorrect-os-mapping"></a>Mapeamento incorreto do SO

Quando uma imagem é criada, pode ser mapeada ou atribuída à etiqueta de SO errada. Por exemplo, quando o **Windows** é selecionado como parte do nome DE enquanto cria a imagem, o disco OS só deve ser instalado com o Windows. O mesmo se aplica ao Linux.

## <a name="vm-not-generalized"></a>VM não generalizado

Se todas as imagens tiradas do Azure Marketplace forem reutilizadas, então o sistema operativo VHD deve ser generalizado.

Linux:

O processo seguinte generaliza um Linux VM e reimplanta-o como um VM separado.

Na janela SSH, insira o seguinte comando:`sudo waagent -deprovision+user`

Windows:

As imagens do Windows são generalizadas com `sysreptool` .

Pode encontrar mais informações sobre esta ferramenta na [Sysprep (System Preparation) Overview]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)

## <a name="datadisk-error"></a>Erro datadisk

Consulte a tabela seguinte para obter soluções para erros relacionados com o disco de dados.

|Erro|reason|solução|
|---|---|---|
|`DataDisk- InvalidUrl:`|Este erro pode ser devido a um número inválido especificado para a LUN durante a apresentação da oferta.|Verifique se a sequência de números LUN para o disco de dados está no centro partner.|
|`DataDisk- NotFound:`|Este erro pode ser devido a um disco de dados não estar localizado num URL SAS especificado|Verifique se o disco de dados está localizado no URL SAS especificado no pedido.|

## <a name="remote-access-issue"></a>Problema de Acesso Remoto

Se a opção RDP não estiver ativada para a imagem do Windows, receberá este erro. 

Ativar o acesso rdp para imagens do Windows antes de submeter.

## <a name="next-steps"></a>Passos Seguintes

Se tiver dúvidas ou feedback para melhorar, contacte [o Partner Center Support](https://partner.microsoft.com/support/v2/?stage=1).
