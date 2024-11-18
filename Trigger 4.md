# Create the object called "Customer Project" and create Status field under this object with picklist data type (Values-Active, Inactive). Create the relationship between this custom object and Opportunity so that Customer Project is related list to the Opportunity. Create Active Customer project- field on Opportunity object (Data Type—Checkbox) When we create Customer Project for an Opportunity with the Status=Active, then Active Customer project check box on the Opportunity Detail page is automatically checked.

## Trigger

trigger CustomerProjectTrigger on Customer_Project__c (before insert, after insert, before update, after update, before delete, after delete, after undelete) {
    
    CustomerProjectTriggerHandler obj = new CustomerProjectTriggerHandler();
    obj.doAction();

}

## Handler using Switch

public class CustomerProjectTriggerHandler {
    
    List<Customer_Project__c> TriggerOld;
    List<Customer_Project__c> TriggerNew;
    Map<Id, Customer_Project__c> TriggerOldMap;
    Map<Id, Customer_Project__c> TriggerNewMap;
    
    Static Set<Id> oppIds = new Set<Id>();
    
    public CustomerProjectTriggerHandler() 
    {
        TriggerOld = (List<Customer_Project__c>)Trigger.old;
        TriggerNew = (List<Customer_Project__c>)Trigger.new;
        TriggerOldMap = (Map<Id, Customer_Project__c>)Trigger.oldmap;
        TriggerNewMap = (Map<Id, Customer_Project__c>)Trigger.newmap;
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
                caseStudy4_changeOppActiveStatus();
            }
            When AFTER_UPDATE
            {
                caseStudy4_changeOppActiveStatus();
            }
            When BEFORE_DELETE
            {
                caseStudy4_BeforeDelete();
            }
            When AFTER_DELETE
            {
                caseStudy4_AfterDelete();
            }
            When AFTER_UNDELETE
            {
                
            }
        }
    }
    
    public void caseStudy4_changeOppActiveStatus()
    {
        List<Opportunity> oppList = new List<Opportunity>();
        
        for(Customer_Project__c record:TriggerNew)
        {
            if(record.Opportunity__c != Null && record.Status__c=='Active')
            {               
                Opportunity opp = new Opportunity();
                opp.Id = record.Opportunity__c;
                opp.Active_Customer_Project__c=true;
                oppList.add(opp);            
            }
        }
        
        if(oppList.size()>0)
        {
            Update oppList;
        }
        
    }
    
    public void caseStudy4_BeforeDelete()
    {
        for(Customer_Project__c record:TriggerOld)
        {
            if(record.Opportunity__c != Null)
            {
                oppIds.add(record.Opportunity__c);
            }
        }
    }
    
    public void caseStudy4_AfterDelete()
    {
        Map<Id, AggregateResult> agResult = new Map<Id, AggregateResult>([SELECT COUNT(Id) countOfCusProj, Opportunity__c Id
                                                                          FROM Customer_Project__c
                                                                          WHERE Opportunity__c IN :oppIds
                                                                          GROUP BY Opportunity__c]);
        
        List<Opportunity> oppListToUpdate = new List<Opportunity>();
        
        for(Id recId : oppIds)
        {
            if(!agResult.containsKey(recId))
            {
                Opportunity opp = new Opportunity();
                opp.Id = recId;
                opp.Active_Customer_Project__c = false;
                oppListToUpdate.add(opp);
            }
        }
        
        if(!oppListToUpdate.isEmpty())
        {
            update oppListToUpdate;
        }
    }
    
}
