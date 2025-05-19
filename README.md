A arquitetura abaixo é baseada em uma estrutura SOA (Service-Oriented Architecture), com ênfase na modularidade, reusabilidade e integração com serviços externos. Essa abordagem permite que cada parte do sistema opere de forma autônoma, mas também colaborativa, facilitando manutenção, escalabilidade e evolução contínua.

<div align = center>
<img src="PROND-PROG.svg" alt="Arquitetura SOA">
</div>

### Fluxo/Passo a Passo do Processo de Reserva no Sistema

Ao utilizar o sistema, um cliente passa por uma sequência de etapas bem definidas que envolvem a interação com os diferentes componentes da arquitetura. Abaixo está o fluxo detalhado com base no diagrama apresentado:

1. **Acesso ao Frontend Cliente**: O usuário acessa a interface web ou mobile (Frontend Cliente), onde pode iniciar uma pesquisa de voos.
2. **Detecção de Localização (opcional)**: Caso desejado, o sistema aciona o Serviço de Localização, que utiliza a API de Geolocalização para identificar automaticamente a cidade de origem do usuário.
3. **Pesquisa de Voos**: O Frontend Cliente invoca o Serviço de Busca de Voos através da interface FlightSearch, fornecendo critérios como origem, destino, data e preferências. Esse serviço consulta tanto o Banco de Dados de Voos quanto a API Externa de Voos para trazer todos os resultados disponíveis.
4. **Seleção e Reserva**: Após selecionar um voo, o usuário inicia o processo de reserva pelo Serviço de Reserva. Se ainda não estiver autenticado, será redirecionado ao Serviço de Autenticação.
5. **Autenticação**: O Serviço de Autenticação valida as credenciais do usuário ou permite o cadastro rápido, utilizando o Banco de Dados de Usuários.
6. **Processamento do Pagamento**: O Serviço de Reserva se comunica com o Serviço de Pagamento externo para processar a transação financeira.
7. **Confirmação da Reserva**: Após o pagamento aprovado, o Serviço de Reserva atualiza o Banco de Dados de Reservas e dispara uma notificação via Serviço de E-mail para o usuário confirmar a reserva.
8. **Gestão Interna (Administração)**: Funcionários da companhia acessam o Frontend Admin e utilizam o Serviço de Gestão de Voos para gerenciar disponibilidade de assentos, alterar informações de voos e consultar dados de clientes.

Esse fluxo demonstra como os serviços se comunicam entre si e com componentes externos de forma organizada, segura e eficiente, graças à arquitetura SOA adotada.


#### Blocos Principais da Arquitetura

1. **Frontend**  
   - Contém duas interfaces: Frontend Cliente (para usuários) e Frontend Admin (para funcionários).  
   - Permite ao usuário pesquisar voos, reservar passagens, realizar cadastro/login e aos administradores gerenciar voos, assentos e dados de clientes.  
   - A separação entre cliente e administração garante segurança e especialização de funções.

2. **Serviços SOA. Os principais serviços são:**
     - *Serviço de Busca de Voos*: responsável por buscar informações sobre voos disponíveis, integrando banco de dados interno e API externa.
     - *Serviço de Reserva*: realiza a reserva de voos, processa pagamentos e envia confirmações via e-mail.
     - *Serviço de Autenticação*: gerencia login e cadastro de usuários.
     - *Serviço de Localização*: detecta automaticamente a localização do usuário através de uma API externa.
     - *Serviço de Gestão de Voos*: usado pelos funcionários para controlar voos, disponibilidade de assentos e dados de clientes.

3. **Bancos de Dados** 
   - Três bancos de dados específicos garantem organização e eficiência:

     Banco de Dados de Voos: armazena informações sobre rotas, horários e preços.

     Banco de Dados de Usuários: mantém os dados pessoais e credenciais dos usuários.

     Banco de Dados de Reservas: registra todas as reservas realizadas.

4. **APIs Externas**  
   - O sistema faz uso de APIs terceirizadas para reduzir custo de desenvolvimento e aumentar funcionalidades:

     API Externa de Voos: traz dados atualizados de voos de outras companhias.

     Serviço de Pagamento: processa transações financeiras com segurança.

     Serviço de E-mail: envia notificações e confirmações automatizadas.

     API de Geolocalização: identifica a localização do usuário automaticamente.

#### **Integração com Serviços Externos**

A utilização de serviços externos foi estratégica para evitar o desenvolvimento de funcionalidades complexas internamente, como sistemas de pagamento ou geolocalização. Além disso, a integração com APIs de voos amplia a oferta de destinos sem exigir infraestrutura própria. Cada serviço externo é acessado por um serviço interno específico, garantindo isolamento e flexibilidade para substituição futura se necessário.

#### **Diagrama da Arquitetura**

O diagrama foi construído utilizando notação UML de componentes, com estereótipos claros (<<service>>, <<external>>, <<database>>) e interfaces bem definidas. Ele mostra claramente como os elementos se comunicam entre si, respeitando os princípios da arquitetura SOA.

#### **Justificação dos Elementos e Ligações da Arquitetura**

- *Frontend ↔ Serviços SOA*: comunicação feita via interfaces bem definidas (ex: FlightSearch, Booking, Auth). Garante baixo acoplamento e fácil manutenção.
- *Serviços SOA ↔ Bancos de Dados*: acesso direto a dados críticos, com responsabilidades bem distribuídas entre os serviços.
- *Serviços SOA ↔ APIs Externas*: permite delegar tarefas complexas a provedores especializados, reduzindo custo e tempo de desenvolvimento.
- *Servidor de Administração*: o Frontend Admin consome diretamente o Serviço de Gestão de Voos, garantindo controle centralizado das operações da companhia.

### **Requisitos Não Funcionais do Sistema**

| Nº | Requisito Não Funcional | Descrição |
|----|--------------------------|-----------|
| 1 | Alta Disponibilidade | O sistema deve estar disponível 24/7, mesmo durante manutenção ou falhas parciais. |
| 2 | Escalabilidade | Deve suportar aumento de carga conforme crescimento de usuários e requisições. |
| 3 | Segurança | Proteger dados sensíveis como credenciais, dados de cartão e informações pessoais contra ataques e vazamentos. |
| 4 | Desempenho | As buscas de voo e reservas devem ser rápidas e responsivas, mesmo sob alta carga. |
| 5 | Manutenibilidade | O sistema deve permitir atualizações e correções fáceis, especialmente em relação a serviços e APIs externas. |

### **Justificativa da Necessidade dos Requisitos e Sua Relação com a Arquitetura SOA**

A arquitetura SOA adotada naturalmente favorece a implementação desses requisitos não funcionais:

- **Alta Disponibilidade**: Como os serviços são independentes, cada um pode ser monitorado, reiniciado ou replicado individualmente, garantindo disponibilidade global mesmo com falhas em partes do sistema.
  
- **Escalabilidade**: A modularidade permite escalar apenas os serviços mais solicitados (como busca de voos ou reserva), sem afetar outros componentes.

- **Segurança**: A divisão em serviços distintos limita o impacto de vulnerabilidades. Além disso, o uso de serviços terceirizados para pagamento e autenticação reduz a exposição de dados sensíveis no sistema interno.

- **Desempenho**: A arquitetura descentralizada permite otimizar cada serviço individualmente. Por exemplo, o Serviço de Busca de Voos pode utilizar cache ou balanceamento de carga para melhorar a velocidade.

- **Manutenibilidade**: Com serviços bem delimitados, é possível atualizar ou substituir componentes sem impactar o todo. Isso é especialmente útil quando há mudanças em APIs externas ou necessidade de novas funcionalidades.

Esses requisitos não funcionais são fundamentais para que o sistema seja robusto, seguro, rápido e sustentável no longo prazo, características essenciais para qualquer plataforma crítica como um sistema de reservas de voos.

Em conclusão, acreditamos que a arquitetura proposta atende plenamente aos requisitos solicitados no desafio, reunindo modularidade, escalabilidade e integração com serviços terceirizados. A utilização de uma estrutura SOA garante flexibilidade e evolução contínua do sistema, além de facilitar a manutenção e substituição de componentes quando necessário. Os requisitos funcionais — como busca de voos, reserva com pagamento, cadastro rápido e interface administrativa — foram contemplados de maneira clara e coerente, assim como os requisitos não funcionais, essenciais para a operação segura e eficiente em escala. Este modelo é altamente aplicável a sistemas reais de companhias aéreas que buscam soluções modernas, integradas e orientadas a serviços.
