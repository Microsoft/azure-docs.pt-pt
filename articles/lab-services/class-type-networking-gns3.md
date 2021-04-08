---
title: Criar um laboratório de networking com a Azure Lab Services e a GNS3 | Microsoft Docs
description: Aprenda a criar um laboratório usando os Serviços Azure Lab para ensinar networking com GNS3.
ms.topic: article
ms.date: 01/19/2021
ms.openlocfilehash: dec5dea13d5a89536a06da45fc57d33881a9b3ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99500516"
---
# <a name="set-up-a-lab-to-teach-a-networking-class"></a>Criar um laboratório para ensinar uma aula de networking 
Este artigo mostra-lhe como configurar uma aula que se foca em permitir que os alunos emularem, configurar, testar e resolver problemas de redes virtuais e reais usando software [GNS3.](https://www.gns3.com/) 

Este artigo tem duas secções principais. A primeira secção cobre como criar o laboratório de sala de aula. A segunda secção abrange como criar a máquina de modelo com virtualização aninhada ativada e com GNS3 instalada e configurada.

## <a name="lab-configuration"></a>Configuração de laboratório
Para montar este laboratório, precisa de uma assinatura Azure para começar. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Assim que tiver uma subscrição do Azure, pode criar uma nova conta de laboratório nos Serviços Azure Lab ou utilizar uma conta existente. Consulte o seguinte tutorial para a criação de uma nova conta de laboratório: [Tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md).

Siga [este tutorial](tutorial-setup-classroom-lab.md) para criar um novo laboratório e, em seguida, aplique as seguintes definições:

| Tamanho da máquina virtual | Imagem |
| -------------------- | ----- | 
| Grande (Virtualização Aninhada) | Windows 10 Pro, Versão 1909 |

## <a name="template-machine"></a>Máquina de modelo 

Após a criação da máquina do modelo, inicie a máquina e ligue-a para completar as três tarefas principais seguintes. 
 
1. Prepare a máquina de modelo para a virtualização aninhada.
2. Instale o GNS3.
3. Criar GNS3 VM aninhado em Hiper-V.
4. Configure gNS3 para utilizar o Windows Hyper-VM.
5. Adicione aparelhos apropriados.
6. Modelo de publicação.


### <a name="prepare-template-machine-for-nested-virtualization"></a>Preparar máquina de modelo para virtualização aninhada
- Siga as instruções [deste artigo](how-to-enable-nested-virtualization-template-vm.md) para preparar a máquina virtual do seu modelo para a virtualização aninhada. 

### <a name="install-gns3"></a>Instalar GNS3
- Siga as instruções para [instalar o GNS3 no Windows](https://docs.gns3.com/docs/getting-started/installation/windows).  Certifique-se de que inclui a instalação do **GNS3 VM** no diálogo do componente, consulte abaixo.

![SelectGNS3vm](./media/class-type-networking-gns3/gns3-select-vm.png)

Eventualmente chegará à seleção GNS3 VM. Certifique-se de selecionar a opção **Hiper-V.**

![SelectHyperV](./media/class-type-networking-gns3/gns3-vm-hyper-v.png)

  Esta opção irá descarregar os ficheiros PowerShell script e VHD para criar o GNS3 VM no gestor Hyper-V. Continue a instalação utilizando os valores predefinidos. **Uma vez concluída a configuração, não inicie o GNS3**.

### <a name="create-gns3-vm"></a>Criar GNS3 VM
Uma vez concluída a configuração, um ficheiro zip **"GNS3.VM.Hyper-V.2.2.17.zip"** é descarregado para a mesma pasta que o ficheiro de instalação, contendo as unidades e o script PowerShell para criar o Hyper-V.
- **Extrair tudo** no GNS3.VM.Hyper-V.2.2.17.zip.  Esta ação extrairá as unidades e o script PowerShell para criar o VM.
- **Corra com PowerShell** no script PowerShell "create-vm.ps1" clicando no ficheiro.
- Um pedido de alteração da política de execução pode aparecer. Introduza "Y" para executar o script.

![PSExecutionPolicy](./media/class-type-networking-gns3/powershell-execution-policy-change.png)

- Uma vez concluído o guião, pode confirmar que o VM "GNS3 VM" foi criado no Hyper-V Manager.

### <a name="configure-gns3-to-use-hyper-v-vm"></a>Configure gNS3 para usar Hiper-V VM
Agora que o GNS3 está instalado e o GNS3 VM é adicionado, inicie o GNS3 para ligar os dois.  O [assistente de configuração GNS3 iniciar-se-á automaticamente.](https://docs.gns3.com/docs/getting-started/setup-wizard-gns3-vm#local-gns3-vm-setup-wizard)  
- Utilize os **aparelhos Run a partir de máquina virtual.** .  Utilize as predefinições para o resto do assistente até que não possa ser encontrada a **ferramenta VMware vmrun.** .

![VMWareError](./media/class-type-networking-gns3/gns3-vmware-vmrun-tool-not-found.png)

- Escolha **Ok** e **cancele** o assistente.
- Para completar a ligação ao Hiper-V vm, abra as Preferências **de Edição**  ->    ->  **GNS3 VM** e selecione **Ative o GNS3 VM** e selecione a opção **Hyper-V.**
 
![AtivarGNS3VMs](./media/class-type-networking-gns3/gns3-preference-vm.png)

### <a name="add-appropriate-appliances"></a>Adicione aparelhos apropriados

Neste ponto, deverá adicionar os [aparelhos apropriados para a aula.](https://docs.gns3.com/docs/using-gns3/beginners/install-from-marketplace)

### <a name="publish-template"></a>Publicar modelo

Agora que o modelo VM está configurado corretamente, e pronto para a publicação, existem alguns pontos-chave para verificar.
- Certifique-se de que o GNS3 VM está desligado ou desligado.  A publicação enquanto o VM ainda está em funcionamento irá corromper o VM.
- Fechar gNS3, publicar enquanto e correr pode levar a efeitos colaterais indesejados.
- Limpe quaisquer ficheiros de instalação ou outros ficheiros desnecessários.

## <a name="cost"></a>Custo  

Se quiser estimar o custo deste laboratório, pode usar o seguinte exemplo: 
 
Para uma turma de 25 alunos com 20 horas de horário de aula programado e 10 horas de quota para trabalhos de casa ou tarefas, o preço para o laboratório seria: 

25 alunos * (20 + 10) horas * 84 Unidades de Laboratório * 0,01 USD por hora = 630 USD. 

**Importante:** A estimativa de custos é apenas para fins.  Para obter detalhes atuais sobre os preços, consulte [o Preço dos Serviços do Laboratório Azure.](https://azure.microsoft.com/pricing/details/lab-services/)

## <a name="conclusion"></a>Conclusão
Este artigo acompanhou-o através dos degraus para criar um laboratório para a configuração da rede usando o GNS3.

## <a name="next-steps"></a>Passos seguintes
Os próximos passos são comuns para a criação de qualquer laboratório:

- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota definida](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Links de inscrição de e-mail para estudantes.](how-to-configure-student-usage.md#send-invitations-to-users)