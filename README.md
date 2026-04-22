---
config:
  layout: elk
---
classDiagram
direction TB
    class CashDesk {
	    +int CashDeskId
	    +string Name
	    +ICollection~Shift~ Shifts
	    +ICollection~CashBalance~ CashBalances
    }

    class Shift {
	    +int ShiftId
	    +int CashDeskId
	    +int UserId
	    +DateTime OpenDate
	    +DateTime? CloseDate
	    +string ShiftNumber
	    +string? CloseComment
	    +CashDesk CashDesk
	    +User User
	    +ICollection~Transaction~ Transactions
	    +ICollection~ShiftOpeningBalance~ OpeningBalances
	    +ICollection~ShiftDiscrepancy~ Discrepancies
    }

    class ShiftOpeningBalance {
	    +int ShiftId
	    +int CurrencyId
	    +decimal OpeningBalance
	    +Shift Shift
	    +Currency Currency
    }

    class ShiftDiscrepancy {
	    +int ShiftId
	    +int CurrencyId
	    +decimal ExpectedBalance
	    +decimal ActualBalance
	    +decimal Difference
	    +Shift Shift
	    +Currency Currency
    }

    class Transaction {
	    +int TransactionId
	    +string TransactionNumber
	    +int ShiftId
	    +string OperationType
	    +int? FromAccountId
	    +int? ToAccountId
	    +int CurrencyId
	    +int? ToCurrencyId
	    +decimal Amount
	    +decimal? AmountInTargetCurrency
	    +int? ExchangeRateId
	    +int? TemporaryClientId
	    +DateTime TransactionDate
	    +string? Description
	    +Shift Shift
	    +Account? FromAccount
	    +Account? ToAccount
	    +Currency Currency
	    +Currency? ToCurrency
	    +ExchangeRate? ExchangeRate
	    +TemporaryClient? TemporaryClient
    }

    class Account {
	    +int AccountId
	    +int ClientId
	    +string AccountNumber
	    +int CurrencyId
	    +decimal Balance
	    +Client Client
	    +Currency Currency
    }

    class Client {
	    +int ClientId
	    +string FullName
	    +string PassportNumber
	    +string Phone
	    +ICollection~Account~ Accounts
    }

    class TemporaryClient {
	    +int TemporaryClientId
	    +string LastName
	    +string FirstName
	    +string MiddleName
	    +string PassportSeriesNumber
	    +DateTime? PassportIssueDate
	    +string? PassportIssuedBy
	    +string Citizenship
	    +DateTime CreatedAt
    }

    class Currency {
	    +int CurrencyId
	    +string Code
	    +string Name
	    +string Symbol
    }

    class ExchangeRate {
	    +int RateId
	    +int FromCurrencyId
	    +int ToCurrencyId
	    +decimal Rate
	    +decimal? Bid
	    +decimal? Ask
	    +DateTime Date
	    +DateTime? EffectiveDate
	    +bool IsActive
	    +int? CreatedByUserId
	    +Currency FromCurrency
	    +Currency ToCurrency
	    +User? CreatedByUser
    }

    class User {
	    +int UserId
	    +string Login
	    +string PasswordHash
	    +string Role
	    +string FullName
    }

    class AuditLog {
	    +long AuditLogId
	    +DateTime CreatedAt
	    +int? UserId
	    +string Role
	    +string Action
	    +string EntityType
	    +string EntityId
	    +string? Details
	    +User? User
    }

    class CashBalance {
	    +int CashDeskId
	    +int CurrencyId
	    +decimal Balance
	    +CashDesk CashDesk
	    +Currency Currency
    }

    CashDesk "1" -- "0..*" Shift : имеет
    CashDesk "1" -- "0..*" CashBalance : содержит
    Shift "1" -- "0..*" Transaction : включает
    Shift "1" -- "0..*" ShiftOpeningBalance : имеет
    Shift "1" -- "0..*" ShiftDiscrepancy : фиксирует
    Shift "0..*" -- "1" User : открыта пользователем
    Transaction "0..*" -- "0..1" Account : FromAccount
    Transaction "0..*" -- "0..1" Account : ToAccount
    Transaction "0..*" -- "1" Currency : валюта операции
    Transaction "0..*" -- "0..1" Currency : целевая валюта
    Transaction "0..*" -- "0..1" ExchangeRate : использует курс
    Transaction "0..*" -- "0..1" TemporaryClient : для разового клиента
    Account "0..*" -- "1" Client : принадлежит
    Account "0..*" -- "1" Currency : валюта счета
    ExchangeRate "0..*" -- "1" Currency : FromCurrency
    ExchangeRate "0..*" -- "1" Currency : ToCurrency
    ExchangeRate "0..*" -- "0..1" User : установлен пользователем
    AuditLog "0..*" -- "0..1" User : ссылается на пользователя
    CashBalance "0..*" -- "1" Currency : валюта остатка
    ShiftOpeningBalance "0..*" -- "1" Currency : валюта начального остатка
    ShiftDiscrepancy "0..*" -- "1" Currency : валюта расхождения
