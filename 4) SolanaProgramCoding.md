Let’s start with the hello world example and go through each segment of the code. 

The full code can be found at: https://github.com/solana-labs/example-helloworld/blob/master/src/program-rust/src/lib.rs

use borsh::{BorshDeserialize, BorshSerialize};
use solana_program::{
    account_info::{next_account_info, AccountInfo},
    entrypoint,
    entrypoint::ProgramResult,
    msg,
    program_error::ProgramError,
    pubkey::Pubkey,
};

/// Define the type of state stored in accounts
#[derive(BorshSerialize, BorshDeserialize, Debug)]  // This is an annotation which allows the Struct 
                                                    // to inherit the specified traits.
pub struct GreetingAccount {
    /// number of greetings
    pub counter: u32,
}

// Declare and export the program's entrypoint
entrypoint!(process_instruction);

We start by doing some standard includes and declaring an entry point which will be the process_instruction function.
Borsh stands for Binary Object Representation Serializer for Hashing.

// Program entrypoint's implementation
pub fn process_instruction(
    program_id: &Pubkey, // Public key of the account the hello world program was loaded into
    accounts: &[AccountInfo], // The account to say hello to
    _instruction_data: &[u8], // Ignored, all helloworld instructions are hellos; _ at the start means 
                              // this will not be used but this is required
) -> ProgramResult {

    msg!("Helloworld Rust program entrypoint");         //println! macro is not supported in Solana
    let accounts_iter = &mut accounts.iter();
    let account = next_account_info(accounts_iter)?;
    
The ProgramResult is where the magic happens and we start by printing a fairly pointless message and then select the accountInfo by looping through although in practice there is likely only one.

     // The account must be owned by the program in order to modify its data
    if account.owner != program_id {
        msg!("Greeted account does not have the correct program id");
        return Err(ProgramError::IncorrectProgramId);
    }

    // Increment and store the number of times the account has been greeted
    let mut greeting_account = GreetingAccount::try_from_slice(&account.data.borrow())?;
    greeting_account.counter += 1;
    greeting_account.serialize(&mut &mut account.data.borrow_mut()[..])?;

    msg!("Greeted {} time(s)!", greeting_account.counter);

    Ok(())
}
Finally we get to the good stuff where we “borrow” the existing appAccount data, increase that value by one and rewrite it back. Then last of course it outputs another fairly pointless “Hello” message.

So the program increments the number stored in accountInfo.data.num_greets by one each time it is run.