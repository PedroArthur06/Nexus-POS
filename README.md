# Point of Sale & Corporate Billing Management System | Nexus POS

![Status](https://img.shields.io/badge/Status-In_Production-success?style=for-the-badge)
![Code](https://img.shields.io/badge/Code-Proprietary-red?style=for-the-badge)
![NestJS](https://img.shields.io/badge/NestJS-E0234E?style=for-the-badge&logo=nestjs&logoColor=white)
![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)

> **A full-stack Point of Sale (POS) and financial management solution developed to automate cash flow and corporate billing for a restaurant.**

---

## 🔒 Ownership Notice
**This is a proprietary commercial project.**
The source code is protected and belongs to the client. This repository serves as a **technical case study** documenting the architecture, challenges, and solutions implemented during development.

---

## 💼 Business Context & Problems Solved

The restaurant faced difficulties with manual control of "credit sales" (agreements with local companies) and daily cash closing, resulting in financial errors and revenue loss.

**My Role:** Full-Stack Software Engineer (Lead).
**The Solution:** I designed and built a centralized platform that manages:
1.  **Billing Cycles:** Automated control of sales for partner companies (e.g., Todimo, Prime) with monthly closing.
2.  **Secure Cash Flow:** Cash Opening and Closing system with overage/shortage auditing.
3.  **Real-Time Reports:** Financial dashboard to track Pix, Cash, iFood, and Card transactions.

---

## 🚀 Key Features

| Feature | Technical Detail |
| :--- | :--- |
| **Corporate Agreement Management** | Logic to group orders from employees of partner companies and generate a single monthly invoice. |
| **Session Control** | Robust authentication with **JWT & Refresh Tokens** and access control (Admin vs Operator). |
| **Financial Audit** | Immutable logs of all transactions (income, expenses, and cash withdrawals). |
| **Dynamic Reports** | Complex database aggregations for daily and monthly closing reports. |

---

## 💻 Architecture Example (Conceptual Snippet)

*Below is an example of the (simplified) service logic used to ensure the integrity of financial transactions:*

```typescript
// Example: Financial movement creation logic
@Injectable()
export class MovementsService {
  constructor(private prisma: PrismaService) {}

  async create(dto: CreateMovementDto, userId: string) {
    // 1. Check if the day's cash register is open
    const report = await this.dailyReportsService.findOpenReport();
    if (!report) throw new BadRequestException("Cash register is closed.");

    // 2. Atomic Transaction: Ensures financial consistency
    return await this.prisma.$transaction(async (tx) => {
      // Creates the movement
      const movement = await tx.movement.create({
        data: { ...dto, reportId: report.id, userId }
      });

      // Updates balance if it's a cash income movement
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

## 🛠️ Stack & Tools
**Backend:** NestJS (Node.js), TypeScript, Prisma ORM.

**Database:** PostgreSQL (Hosted on Supabase).

**Frontend:** React, Vite, Context API, CSS Modules.

**DevOps:** Docker, Render (Backend), Vercel (Frontend).

**Design Patterns:** Repository Pattern, Dependency Injection, DTOs.

---

## 👨‍💻 Developer
Pedro Arthur
Software Engineer | Full Stack
