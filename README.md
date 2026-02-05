# Sistema Caixa & Gestão de Convênios | Nexus POS

![Status](https://img.shields.io/badge/Status-Em_Produção-success?style=for-the-badge)
![Code](https://img.shields.io/badge/Code-Proprietary-red?style=for-the-badge)
![NestJS](https://img.shields.io/badge/NestJS-E0234E?style=for-the-badge&logo=nestjs&logoColor=white)
![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)

> **Uma solução full-stack de Ponto de Venda (PDV) e gestão financeira desenvolvida para automatizar o fluxo de caixa e o faturamento corporativo de um restaurante.**

---

## 🔒 Aviso de Propriedade
**Este é um projeto comercial proprietário.**
O código-fonte é protegido e pertence ao cliente. Este repositório serve como um **estudo de caso técnico** documentando a arquitetura, desafios e soluções implementadas durante o desenvolvimento.

---

## 💼 Contexto de Negócio & Problemas Resolvidos

O restaurante enfrentava dificuldades com o controle manual de "vendas a prazo" (convênios com empresas locais) e fechamento de caixa diário, resultando em erros financeiros e perda de receita.

**Meu Papel:** Engenheiro de Software Full-Stack (Lead).
**A Solução:** Projetei e construí uma plataforma centralizada que gerencia:
1.  **Ciclos de Faturamento:** Controle automático de vendas para empresas parceiras (ex: Todimo, Prime) com fechamento mensal.
2.  **Fluxo de Caixa Blindado:** Sistema de Abertura e Fechamento de caixa com auditoria de sobras/faltas.
3.  **Relatórios em Tempo Real:** Dashboard financeiro para acompanhar Pix, Dinheiro, iFood e Cartão.

---

## 🚀 Funcionalidades Chave

| Funcionalidade | Detalhe Técnico |
| :--- | :--- |
| **Gestão de Convênios** | Lógica para agrupar pedidos de funcionários de empresas parceiras e gerar fatura única mensal. |
| **Controle de Sessão** | Autenticação robusta com **JWT & Refresh Tokens** e controle de acesso (Admin vs Operador). |
| **Auditoria Financeira** | Logs imutáveis de todas as movimentações (entradas, saídas e sangrias). |
| **Relatórios Dinâmicos** | Agregações complexas no banco de dados para relatórios de fechamento diário e mensal. |

---

## 💻 Exemplo de Arquitetura (Snippet Conceitual)

*Abaixo, um exemplo da lógica de serviço (simplificada) utilizada para garantir a integridade das movimentações financeiras:*

```typescript
// Exemplo: Lógica de criação de movimentação financeira
@Injectable()
export class MovementsService {
  constructor(private prisma: PrismaService) {}

  async create(dto: CreateMovementDto, userId: string) {
    // 1. Valida se o caixa do dia está aberto
    const report = await this.dailyReportsService.findOpenReport();
    if (!report) throw new BadRequestException("Caixa fechado.");

    // 2. Transação Atômica: Garante consistência financeira
    return await this.prisma.$transaction(async (tx) => {
      // Cria a movimentação
      const movement = await tx.movement.create({
        data: { ...dto, reportId: report.id, userId }
      });

      // Atualiza saldo se for movimentação em dinheiro
      if (dto.type === 'INCOME_CASH') {
        await tx.dailyReport.update({
          where: { id: report.id },
          data: { currentBalance: { increment: dto.amount } }
        });
      }

      return movement;
    });
  }
}
```
---

## 🛠️ Stack & Ferramentas
**Backend:** NestJS (Node.js), TypeScript, Prisma ORM.

**Banco de Dados:** PostgreSQL (Hospedado no Supabase).

**Frontend:** React, Vite, Context API, CSS Modules.

**DevOps:** Docker, Render (Backend), Vercel (Frontend).

**Design Patterns:** Repository Pattern, Dependency Injection, DTOs.

---

## 👨‍💻 Desenvolvedor
### Pedro Arthur
Software Engineer | Full Stack
