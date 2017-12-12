Armazenamento localmente redundante (LRS) foi concebido para fornecer, pelo menos, 99.999999999% (11 da 9) durabilidade dos objetos através de um ano fornecido pelo replicar os dados dentro de uma unidade de escala de armazenamento, que está alojado num centro de dados na região na qual criou a conta de armazenamento. Um pedido de escrita com êxito devolve apenas depois de ter sido escrito para todas as réplicas. Estas réplicas residirem em domínios de falhas separada e domínios de atualização na unidade de escala de um armazenamento.

Uma unidade de escala de armazenamento é uma coleção de bastidores de nós de armazenamento. Um domínio de falhas (DF) é um grupo de nós que representam uma unidade física de falha e pode ser considerada como nós que pertencem ao mesmo bastidor físico. Um domínio de atualização (UD) é um grupo de nós que sejam atualizados em conjunto durante o processo de uma atualização do serviço (implementação). As réplicas são distribuídas por UDs e FDs dentro de uma unidade de escala de armazenamento para garantir que os dados estão disponíveis mesmo se a falha de hardware afeta um bastidor único ou quando nós sejam atualizados durante uma implementação.

LRS é a opção de custo mais baixa e oferece uma durabilidade, pelo menos, em comparação comparada outras opções. Em caso de desastre ao nível do Centro de dados (fire, sobrecarregar etc.), todas as réplicas podem ser perdido ou irrecuperável. Para mitigar este risco, recomenda-se o armazenamento redundante de Georreplicação (GRS) para a maioria das aplicações.

Armazenamento localmente redundante ainda pode ser preferível em determinados cenários:

* Fornece maior largura de banda máxima das opções de replicação de armazenamento do Azure.
* Se a aplicação armazena os dados que é possível facilmente reconstruir, poderá optar por LRS.
* Algumas aplicações estão limitadas a replicar dados apenas dentro de um país devido a requisitos de governação de dados. Pode ser uma região emparelhada noutro país. Para obter mais informações sobre os pares de região, consulte [regiões do Azure](https://azure.microsoft.com/regions/).
