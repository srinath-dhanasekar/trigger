# Create the object called "Customer" and create the Master-Detail Relationship on Customer object so that Customer will be the related list to Account record. Create the field called "Account Manager" on Customer object which is lookup to the user object. When we create Customer record for account record, then the user in Account Manager field will be automatically added to Account Team of that associated account.

# Trigger

trigger CustomerTrigger on Customer__c (after insert) {
    
    CustomerTriggerHandler obj = new CustomerTriggerHandler();
    obj.doAction();

}

# Handler using Switch

public class CustomerTriggerHandler {
    
    List<Customer__c> TriggerOld;
    List<Customer__c> TriggerNew;
    Map<Id, Customer__c> TriggerOldMap;
    Map<Id, Customer__c> TriggerNewMap;
    
    public CustomerTriggerHandler() 
    {
        TriggerOld = (List<Customer__c>)Trigger.old;
        TriggerNew = (List<Customer__c>)Trigger.new;
        TriggerOldMap = (Map<Id, Customer__c>)Trigger.oldmap;
        TriggerNewMap = (Map<Id, Customer__c>)Trigger.newmap;
    }
    
    public void doAction()
    {
        Switch on trigger.OperationType
        {
            When BEFORE_INSERT
            {
                
            }
            When BEFORE_UPDATE
            {

            }
            When AFTER_INSERT
            {
								caseStudy6_PopulateAccountManager();
            }
            When AFTER_UPDATE
            {

            }
            When BEFORE_DELETE
            {
                
            }
            When AFTER_DELETE
            {
                
            }
            When AFTER_UNDELETE
            {
                
            }
        }
    }
    
    public void caseStudy6_PopulateAccountManager()
    {
        List<AccountTeamMember> atmList = new List<AccountTeamMember>();
        
        for(Customer__c rec:TriggerNew)
        {
            if(rec.Account__c != Null)
            {
                AccountTeamMember member = new AccountTeamMember();
                member.AccountId = rec.Account__c;
                member.TeamMemberRole = 'Account Manager';
                member.UserId = rec.Account_Manager__c;
                atmList.add(member);
            }
        }
        
        if(atmList.size()>0)
        {
            Insert atmList;
        }
    }

trigger CustomerTrigger on Customer__c (after insert) {
    
    CustomerTriggerHandler obj = new CustomerTriggerHandler();
    obj.doAction();

}
