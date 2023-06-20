# Foodcart
//code that reads a log file containing eater_id and foodmenu_id entries and returns the top 3 menu items consumed.
package main

import (
	"bufio"
	"fmt"
	"os"
	"strings"
)

type MenuItem struct {
	MenuID  string
	Counter int
}

func main() {
	// Read the log file
	file, err := os.Open("Menu.txt") 
	if err != nil {
		fmt.Println("Error opening log file:", err)
		return
	}
	defer file.Close()

	// Map to store menu items and their counters
	menuItems := make(map[string]int)

	// Read the log file line by line
	scanner := bufio.NewScanner(file)
	for scanner.Scan() {
		line := scanner.Text()
		fields := strings.Fields(line)
		if len(fields) == 2 {
			eaterID := fields[0]
			foodMenuID := fields[1]

			// Check for duplicate entries
			if menuItems[foodMenuID] > 0 {
				fmt.Println("Error: Duplicate entry for eater_id", eaterID, "and foodmenu_id", foodMenuID)
				return
			}

			// Increment the counter for the menu item
			menuItems[foodMenuID]++
		}
	}

	// Close the scanner and check for any scanning errors
	if err := scanner.Err(); err != nil {
		fmt.Println("Error reading log file:", err)
		return
	}

	// Find the top 3 menu items consumed
	topMenuItems := findTopMenuItems(menuItems, 3)

	// Print the top menu items
	fmt.Println("Top 3 Menu Items Consumed:")
	for _, menuItem := range topMenuItems {
		fmt.Printf("Menu ID: %s, Count: %d\n", menuItem.MenuID, menuItem.Counter)
	}
}

// Function to find the top n menu items consumed
func findTopMenuItems(menuItems map[string]int, n int) []MenuItem {
	topMenuItems := make([]MenuItem, 0, n)
	for menuID, counter := range menuItems {
		topMenuItems = append(topMenuItems, MenuItem{MenuID: menuID, Counter: counter})
	}

	// Sort the menu items based on the counter in descending order
	sortByCounter(topMenuItems)

	// Limit the result to the top n items
	if len(topMenuItems) > n {
		topMenuItems = topMenuItems[:n]
	}

	return topMenuItems
}

// Function to sort the menu items based on the counter in descending order
func sortByCounter(menuItems []MenuItem) {
	for i := 0; i < len(menuItems)-1; i++ {
		for j := 0; j < len(menuItems)-i-1; j++ {
			if menuItems[j].Counter < menuItems[j+1].Counter {
				menuItems[j], menuItems[j+1] = menuItems[j+1], menuItems[j]
			}
		}
	}
}
